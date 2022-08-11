<template>
  <v-container fluid mt-2>
    <v-card class="elevation-12" min-height="100%">
      <v-toolbar dark color="primary">
        <v-toolbar-title>Create secret</v-toolbar-title>
      </v-toolbar>
      <v-card-text>
        <v-form ref="secretForm">
          <v-text-field v-model="secretText" :rules="secretRules" type="string" name="secretText" label="Secret Text" hint="Text to hide" required></v-text-field>
          <br>
          <v-text-field v-model="expireAfterViews" :rules="expireAfterViewsRule" pattern="\d+" min="0" max="8" type="number" label="Expire After Views" required name="expireAfterViews" hint="Set a limit for views"></v-text-field>
          <br>
          <v-text-field v-model="expireAfter" :rules="expireAfterRule" pattern="\d+" min="0" max="8" type="number" label="Expire after minutes" required name="expireAfter" hint="Set a limit in minutes (Set zero for no limit)"></v-text-field>
        </v-form>
      </v-card-text>
      <v-card-actions>
        <span>{{ hash }}</span>
        <v-spacer></v-spacer>
        <v-btn :loading="loading" color="primary" @click="createSecret">{{ loading ? "Loading" : success ? "Secret Created" : "Create Secret" }}</v-btn>
      </v-card-actions>
    </v-card>
  </v-container>
</template>
<script>
export default {
  data: () => ({
    secretText: '',
    expireAfterViews: 0,
    expireAfter: 0,
    loading: false,
    hash: '',
    success: false,
    secretRules: [
      v => !!v || 'Secret text is required',
    ],
    expireAfterViewsRule: [
      v => v === "" ? 'Expire after views is required' : true,
      v => v > 0 || 'Expire after views must be bigger than 0',
    ],
    expireAfterRule: [
      v =>  v === "" ? 'Expire after is required' : true,
      v => v >= 0 || 'Expire after must be bigger or equal to 0',
    ],
  }),
  methods: {
    createSecret: async function () {
      if (this.$refs.secretForm.validate()) {
        this.loading = true;
        fetch("https://secret-server-task-app.herokuapp.com/v1/secret", {
          body: `secret=${this.secretText}&expireAfterViews=${this.expireAfterViews}&expireAfter=${this.expireAfter}`,
          headers: {
            "Content-Type": "application/x-www-form-urlencoded"
          },
          method: "post",
        })
          .then(response => response.json())
          .then((e) => {
            console.log(e);
            this.hash = "Hash: " + e.hash;
            this.loading = false;
            this.success = true;
            setTimeout(() => {
              this.success = false;
            }, 1000);
          })
          .catch((error) => {
            console.error(error);
          });
        return;

      }
    }
  }
}
</script>