<template>
  <div>
    <div
      v-if="allCredentialsSet"
      class="accordion"
      role="tablist"
    >
      <b-form
        inline
        class="form"
      >
        <label for="surveySelect">Select Survey
          <b-form-select
            id="surveySelect"
            v-model="selectedSurvey"
            :options="surveys"
            value-field="surveyID"
            text-field="surveyName"
          />
        </label>
      </b-form>

      <b-card
        no-body
        class="mb-1"
      >
        <b-card-header
          header-tag="header"
          class="p-1"
          role="tab"
        >
          <b-button
            block
            v-b-toggle.accordion-1
            variant="info"
          >
            sendSurvey
          </b-button>
        </b-card-header>
        <b-collapse
          id="accordion-1"
          visible
          accordion="my-accordion"
          role="tabpanel"
        >
          <b-card-body>
            <b-container fluid>
              <b-row
                class="my-1"
              >
                <b-col sm="6">
                  Name
                </b-col>
                <b-col sm="6">
                  E-mail
                </b-col>
              </b-row>
              <b-row
                class="my-1"
                v-for="(recipent, index) in recipents"
                :key="index"
              >
                <b-col sm="6">
                  <b-form-input
                    v-model="recipent.name"
                    type="text"
                  />
                </b-col>
                <b-col sm="6">
                  <b-form-input
                    v-model="recipent.email"
                    type="email"
                  />
                </b-col>
              </b-row>
            </b-container>
            <b-form-checkbox
              v-model="isSandbox"
              name="check-button"
              switch
            >
              Use account email (sandbox)
            </b-form-checkbox>
            <b-button
              variant="outline-dark"
              @click="sendSurvey"
            >
              Proceed
            </b-button>
            <b-row
              class="my-1"
            >
              <b-col sm="6">
                Response
              </b-col>
              <b-col sm="6">
                Request
              </b-col>
            </b-row>
            <b-row
              class="my-1"
            >
              <b-col sm="6">
                <vue-json-pretty
                  v-if="res1!==null"
                  :path="'res1'"
                  :data="res1"
                />
              </b-col>
              <b-col sm="6">
                <vue-json-pretty
                  v-if="req1!==null"
                  :path="'req1'"
                  :data="req1"
                />
              </b-col>
            </b-row>
          </b-card-body>
        </b-collapse>
      </b-card>
      <b-card
        no-body
        class="mb-1"
      >
        <b-card-header
          header-tag="header"
          class="p-1"
          role="tab"
        >
          <b-button
            block
            v-b-toggle.accordion-2
            variant="info"
          >
            getStatus
          </b-button>
        </b-card-header>
        <b-collapse
          id="accordion-2"
          visible
          accordion="my-accordion"
          role="tabpanel"
        >
          <b-card-body>
            <b-button
              variant="outline-dark"
              @click="getStatus"
            >
              Proceed
            </b-button>
            <b-row
              class="my-1"
            >
              <b-col sm="6">
                Response
              </b-col>
              <b-col sm="6">
                Request
              </b-col>
            </b-row>
            <b-row
              class="my-1"
            >
              <b-col sm="6">
                <vue-json-pretty
                  v-if="res2!==null"
                  :path="'res2'"
                  :data="res2"
                />
              </b-col>
              <b-col sm="6">
                <vue-json-pretty
                  v-if="req2!==null"
                  :path="'req2'"
                  :data="req2"
                />
              </b-col>
            </b-row>
          </b-card-body>
        </b-collapse>
      </b-card>
    </div>
    <div v-else>
      Credentials are missing!
    </div>
  </div>
</template>

<script>
import VueJsonPretty from "vue-json-pretty";
import * as sha1 from "sha1";
import * as axios from "axios";

export default {
	name: "Invitations",
	components: {VueJsonPretty},
	props: {
		userID: {
			type: [Number, String],
			default: null
		},
		email: {
			type: String,
			default: null
		},
		apiKey: {
			type: String,
			default: null
		}
	},
	data: function () {
		return {
			res1: null,
			req1: null,
			res2: null,
			req2: null,
			recipents: [{}, {}],
			isSandbox: false,
			surveys: [],
			selectedSurvey: null
		}
	},
	created() {
		this.getSurveys()
	},
	computed: {
		allCredentialsSet() {
			if (this.userID == null) return false
			if (this.email === null) return false
			if (this.apiKey === null) return false
			return true
		},
	},
	methods: {
		getSurveys() {
			let that = this
			let Method = "getSurveys";
			let dateobj = new Date();
			let B = dateobj.toISOString();
			let hash = sha1(Method + that.email + B + that.apiKey);
			let requestBody = {
				'method': Method,
				'authentication': {
					'accountID': that.userID,
					'username': that.email,
					'hash': hash,
					'datetime': B
				},
				'take': 20,
				'skip': 0
			}
			axios.post('https://.surveymechanics.com/api/surveys', JSON.stringify(requestBody))
				.then(({data}) => {
					data.data.surveys.forEach((x) => {
						that.surveys.push(x)
					})
					if (that.surveys.length < data.data.total) that.getSurveys()
				});
		},
		sendSurvey() {
			let that = this
			let Method = "sendSurvey";
			let dateobj = new Date();
			let B = dateobj.toISOString();
			let hash = sha1(Method + this.email + B + this.apiKey);
			let requestBody = {
				'method': Method,
				'authentication': {
					'accountID': this.userID,
					'username': this.email,
					'hash': hash,
					'datetime': B
				},
				'recipents': that.recipents,
				'surveyID': that.selectedSurvey
			}
			if (that.isSandbox) requestBody.sandbox = true
			axios.post('https://.surveymechanics.com/api/invitations', JSON.stringify(requestBody))
				.then(({data}) => {
					that.res1 = data
					that.req1 = requestBody
				});
		},
		getStatus() {
			let that = this
			let Method = "getStatus";
			let dateobj = new Date();
			let B = dateobj.toISOString();
			let hash = sha1(Method + this.email + B + this.apiKey);
			let requestBody = {
				'method': Method,
				'authentication': {
					'accountID': this.userID,
					'username': this.email,
					'hash': hash,
					'datetime': B
				},
				'surveyID': that.selectedSurvey,
				'take': 250,
				'skip': 0
			}
			
			axios.post('https://.surveymechanics.com/api/invitations', JSON.stringify(requestBody))
				.then(({data}) => {
					that.res2 = data
					that.req2 = requestBody
				});
		}
	}
}
</script>

<style scoped>
.form {
	margin: 0 auto;
	margin-top: 50px;
	margin-bottom: 50px;
}
</style>
