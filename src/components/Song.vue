<template>
    <v-card>
        <v-card-title>
            {{name}}
        </v-card-title>
        <v-card-subtitle>
            {{artist}}
        </v-card-subtitle>
        <v-card-text>
            Total Plays: {{count}}
        </v-card-text>
        <v-card-actions>
            <v-btn text @click="go">
                Listen 
            </v-btn>
        </v-card-actions>
    </v-card>
</template>
<script>
export default {
    props: {
        name: String, 
        artist: String, 
        titleUrl: String,
        count: Number
    },
    methods: {
        go() {
            window.open(this.titleUrl);
        }
    }
}
</script>   