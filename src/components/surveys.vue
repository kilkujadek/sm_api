<template>
  <div>
    <div
      v-if="allCredentialsSet"
      class="accordion"
      role="tablist"
    >
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
            getSurveys
          </b-button>
        </b-card-header>
        <b-collapse
          id="accordion-1"
          visible
          accordion="my-accordion"
          role="tabpanel"
        >
          <b-card-body>
            <b-button
              variant="outline-dark"
              @click="getSurveys"
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
    </div>
    <div v-else>
      Credentials are missing!
    </div>
  </div>
</template>

<script>
import * as axios from "axios";
import * as sha1 from "sha1";
import VueJsonPretty from 'vue-json-pretty'
import 'vue-json-pretty/lib/styles.css'


export default {
	name: "SurveysGetSurveys",
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
		}
	},
	computed: {
		allCredentialsSet() {
			if (this.userID==null) return false
			if (this.email===null) return false
			if (this.apiKey===null) return false
			return true
		},
	},
	methods: {
		getSurveys: function () {
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
				'take': 50,
				'skip': 0
			}
			axios.post('https://surveymechanics.com/api/surveys', JSON.stringify(requestBody))
				.then(({data}) => {
					that.res1 = data
					that.req1 = requestBody
				});
		}
	}
}
</script>

<style scoped>

</style>
