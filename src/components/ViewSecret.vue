<template>
    <v-container fluid mt-2>
        <v-card class="elevation-12" min-height="100%">
            <v-toolbar dark color="primary">
                <v-toolbar-title>View secret</v-toolbar-title>
            </v-toolbar>
            <v-card-text>
                <v-form ref="secretViewForm">
                    <v-text-field v-model="hash" :rules="secretHash" type="string" name="secretText" label="Hash Text" hint="Hash" required></v-text-field>
                </v-form>
                <v-list disabled :style="{ display: visible ? '' : 'none' }">
                    <v-subheader>Data</v-subheader>
                    <v-list-item-group color="primary">
                        <v-list-item>
                            <v-list-item-icon>
                                <v-icon>mdi-format-text</v-icon>
                            </v-list-item-icon>
                            <v-list-item-content>
                                <v-list-item-title>Secret text: {{ data.secretText }}</v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                        <v-list-item>
                            <v-list-item-icon>
                                <v-icon>mdi-pound</v-icon>
                            </v-list-item-icon>
                            <v-list-item-content>
                                <v-list-item-title>Hash: {{ data.hash }}</v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                        <v-list-item>
                            <v-list-item-icon>
                                <v-icon>mdi-clock</v-icon>
                            </v-list-item-icon>
                            <v-list-item-content>
                                <v-list-item-title>Expires at: {{ data.expiresAt }}</v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                        <v-list-item>
                            <v-list-item-icon>
                                <v-icon>mdi-clock</v-icon>
                            </v-list-item-icon>
                            <v-list-item-content>
                                <v-list-item-title>Created at: {{ data.createdAt }}</v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                        <v-list-item>
                            <v-list-item-icon>
                                <v-icon>mdi-clock</v-icon>
                            </v-list-item-icon>
                            <v-list-item-content>
                                <v-list-item-title>Remaining views at: {{ data.remainingViews }}</v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                    </v-list-item-group>
                </v-list>
            </v-card-text>
            <v-card-actions>
                <span>{{ info }}</span>
                <v-spacer></v-spacer>
                <v-btn :loading="loading" color="primary" @click="loadSecret">Load secret</v-btn>
            </v-card-actions>
        </v-card>
    </v-container>
</template>
<script>
export default {
    data: () => ({
        info: "",
        loading: false,
        hash: '',
        visible: false,
        data: {
            hash: '',
            secretText: '',
            expiresAt: '',
            createdAt: '',
            remainingViews: 0,
        },
        secretHash: [
            v => !!v || 'Secret hash',
        ],
    }),
    methods: {
        loadSecret: function () {
            if (this.$refs.secretViewForm.validate()) {
                this.loading = true;
                this.visible = false;
                fetch("https://secret-server-task-app.herokuapp.com/v1/secret/" + this.hash, {
                    headers: {
                        "Content-Type": "application/x-www-form-urlencoded"
                    },
                    method: "get",
                })
                    .then(response => response.json())
                    .then((e) => {
                        if (e.message != undefined) {
                            this.info = e.message;
                        } else {
                            this.visible = true;
                            this.info = "Secret Found";
                            this.data = e
                            let createdAt = new Date(e.createdAt);
                            this.data.createdAt = `${createdAt.getHours()}:${createdAt.getMinutes()}:${createdAt.getSeconds()}`;
                            let expiresAt = new Date(e.expiresAt);
                            this.data.expiresAt = `${expiresAt.getHours()}:${expiresAt.getMinutes()}:${expiresAt.getSeconds()}`;
                        }
                        this.loading = false;
                    })
                    .catch((error) => {
                        this.loading = false;
                        this.info = error.message;
                        console.error(error);
                    });
            }
        }
    }
}
</script>