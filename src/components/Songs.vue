<template>
    <div>
        <v-row>
            <v-col lg='4' sm='12'>
                <v-alert color="success" v-if="totalSongPlays">
                    <span class="headline">
                        <span class="text-uppercase">{{ totalSongs }}</span><br/>
                        <span class="text-lowercase">unique {{ totalSongPlays > 1 ? 'songs' : 'song' }} played</span>
                    </span><br/>
                </v-alert>
            </v-col>
            <v-col lg='4' sm='12'>
                <v-alert color="blue" v-if="totalSongPlays">
                    <span class="headline">
                        <span class="text-uppercase">{{ totalSongPlays }}</span><br/>
                        <span class="text-lowercase">total {{ totalSongPlays > 1 ? 'songs' : 'song' }} played</span>
                    </span><br/>
                </v-alert>
            </v-col>
            <v-col lg='4' sm='12'>
                <v-alert cols='4' color="pink" v-if="totalSongPlays">
                    <span class="headline">
                        <span class="text-uppercase">{{ totalSongPlays * 3 }}</span><br/>
                        <span class="text-lowercase">est. minutes listened</span>
                    </span><br/>
                </v-alert>
            </v-col>
        </v-row>
        <template v-for="song in range">
            <song v-bind="song" :key="song.titleUrl"/>
        </template>
        <v-pagination v-model="page" :length="Math.floor(songs.length/10)"/>
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
            return this.songs.slice(this.page *10, this.page *10 + 10);
        }, 
        totalSongs() {
            return this.songs.length;
        },
        totalSongPlays() {
            return this.songs.reduce((acc, song) => acc + song.count, 0);
        }
    }
}
</script>