<template>
  <div>
    <b-input-group
		
      class="mb-5"
    >
      <b-input-group-prepend is-text />
      <b-form-input
        placeholder="UserID"
        v-model="userId"
      />
      <b-form-input
        placeholder="Email address"
        v-model="userEmail"
      />
      <b-form-input
        placeholder="API key"
        v-model="apiKey"
        type="password"
      />
      <b-input-group-append is-text />
    </b-input-group>
    <div>
      <b-tabs content-class="mt-3">
        <b-tab
          title="Surveys"
          active
        >
          <surveys-get-surveys
            :user-i-d="userId"
            :email="userEmail"
            :api-key="apiKey"
          />
        </b-tab>
        <b-tab title="Invitations">
          <invitations
            :user-i-d="userId"
            :email="userEmail"
            :api-key="apiKey"
          />
        </b-tab>
      </b-tabs>
    </div>
  </div>
</template>

<script>
import surveysGetSurveys from "@/components/surveys";
import invitations from "@/components/invitations";

export default {
	name: "Sm",
	data: function () {
		return {
			userId: null,
			userEmail: '',
			apiKey: '',
		}
	},
	components: {surveysGetSurveys, invitations},
}
</script>

<style scoped>

</style>

