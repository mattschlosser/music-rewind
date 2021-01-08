<template>
    <div>
        <v-pagination v-model="page" :length="Math.floor(songs.length/10)+1"/>
        <template v-for="song in range">
            <song v-bind="song" :key="song.titleUrl"/>
        </template>
    </div>
</template>
<script>
import Song from './Song';
export default {
    components: {
        Song
    }, 
    props: {
        songs: Array
    },
    data: () => ({
        page: 1
    }), 
    computed: {
        range() {
            return this.songs.slice(this.page, this.page + 10);
        }
    }
}
</script>