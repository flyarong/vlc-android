<template>
    <div v-on:click="manageClick" class="ratio clickable" v-bind:class="(mainImgClasses())">
        <img v-lazy="$getImageUrl(media, this.mediaType+'_big')" class="media-img-top">
        <div class="media-overlay" v-show="!isBrowse()">
            <img class="overlay-play" :src="(`./icons/play_circle_white.svg`)" width="48" />
        </div>
        <span v-if="(mediaType == 'video' && media.resolution != '')" class="resolution">{{ media.resolution }}</span>
        <img class="played" :src="(`./icons/played.svg`)" v-show="(media.played && mediaType == 'video')"/>

        <div class="card-progress-container" v-show="(media.progress > 0)">
            <div class="card-progress full"></div>
            <div class="card-progress" v-bind:style="(getProgressStyle())"></div>
        </div>
    </div>
    <div class="d-flex">

        <div class="card-body media-text flex1">
            <h6 class="card-title text-truncate">{{ media.title }}</h6>
            <p class="card-text text-truncate subtitle">{{ (mediaType == 'video') ? $readableDuration(media.length) : media.artist }}
            </p>

        </div>
        <div class="dropdown dropstart overlay-more-container" v-show="!this.hideOverflow">
            <ImageButton type="more_vert" id="dropdownMenuButton1" data-bs-toggle="dropdown" aria-expanded="false" />
            <ul class="dropdown-menu media-more" aria-labelledby="dropdownMenuButton1">
                <li> <span v-on:click="$play(media, this.mediaType, false, false)" class="dropdown-item"
                        v-t="'PLAY'"></span> </li>
                <li> <span v-on:click="$play(media, this.mediaType, true, false)" class="dropdown-item"
                        v-t="'APPEND'"></span> </li>
                <li> <span v-if="(mediaType == 'video')" v-on:click="$play(media, this.mediaType, false, true)"
                        class="dropdown-item" v-t="'PLAY_AS_AUDIO'"></span> </li>
                <li v-if="(mediaType != 'file' && mediaType != 'folder')"> <span
                        v-on:click="$download(media, this.mediaType, this.downloadable)" class="dropdown-item"
                        v-t="'DOWNLOAD'"></span>
                </li>

            </ul>
        </div>
    </div>
</template>

<script>
import ImageButton from './ImageButton.vue'
export default {
    components: {
        ImageButton,
    },
    methods: {
        mainImgClasses() {
            if (this.mediaType == 'video') return 'ratio-16x9 video audio-img-container'
            if (this.isBrowse() || this.mediaType == "file") return 'ratio-1x1 audio-img-container'
            return 'ratio-1x1 audio-img-container'
        },
        getProgressStyle() {
            if (this.mediaType == 'video' && this.media.progress > 0 && this.media.length > 0) {
                return `width: ${this.media.progress * 100 / this.media.length}%`
            }
            return ''
        },
        isBrowse() {
            return (this.mediaType == 'folder' || this.mediaType == 'network' || this.mediaType == 'stream' || this.mediaType == 'new-stream')
        },
        manageClick() {
            if (['folder', 'storage', 'network'].includes(this.mediaType)) {
                this.$router.push({ name: 'BrowseChild', params: { browseId: this.media.path } })
            } else {
                this.$play(this.media, this.mediaType)
            }
        }
    },
    props: {
        media: Object,
        downloadable: Boolean,
        mediaType: String,
        hideOverflow: {
            type: Boolean,
            default: false
        }
    },
}
</script>