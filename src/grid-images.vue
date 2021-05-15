<template>
    <div v-if="items.length > 0"
         class="gi-container">
        <div class="gi-grid"
             :class="[css,items.length>cells?'gi-grid-' + cells: 'gi-grid-' + items.length]">
            <a v-for="(media, i) in items"
               :key="i"
               class="gi-item"
               :href="items[i].url"
               role="link"
               @click.prevent="show(i, media)"
               :style="bg(media)">
                <span v-if="i<cells">
                    <span v-if="i === cells-1 && items.length - cells>0"
                          class="gi-more">
                        {{ items.length - cells}}+
                    </span>
                </span>
            </a>
        </div>

        <transition name="fade">
            <div v-if="index >= 0"
                 class="gi-modal">
                <div class="gi-modal-close"
                        @click="close">
                    <IconClose/>
                </div>
                <button class="gi-modal-prev gi-btn"
                        @click="prev">
                    <IconArrow style="transform: rotate(180deg)"/>
                </button>
                <button class="gi-modal-next gi-btn"
                        @click="next">
                    <IconArrow/>
                </button>

                <div class="gi-modal-img"
                     @click="close">
                    <img :src="src" alt="">
                </div>
            </div>
        </transition>
    </div>
</template>

<script>
    import {defineComponent} from 'vue';
    import IconArrow from '@/components/IconArrow.vue';
    import IconClose from '@/components/IconClose.vue';

    export default defineComponent({
        name: 'GridImages',

        props: {
            items: {
                type: Array,
                default: () => {
                    return []
                }
            },

            css: {
                type: String,
                default: () => 'h-250 h-md-400 h-lg-600'
            },

            cells: {
                type: Number,
                default: () => 5
            },
        },

        components: {
            IconArrow,
            IconClose
        },

        data() {
            return {
                src: '',
                index: -1,
                loading: false,
                events: [],
                selectedItem: null,
            }
        },

        methods: {
            bind() {
                if (this.events.length > 0) return;

                this.events.push(['keydown', e => {
                    if (this.index < 0) return;
                    if (e.keyCode === 37) {
                        this.prev();
                    } else if (e.keyCode === 39) {
                        this.next();
                    } else if (e.keyCode === 27) {
                        this.close();
                    }
                }]);
                this.events.forEach(e => {
                    window.addEventListener(e[0], e[1]);
                });
            },

            show(i) {
                if (i >= this.items.length) {
                    i = 0;
                }
                if (i < 0) {
                    i = this.items.length - 1;
                }
                this.loading = true;
                this.bind();
                this.index = i;
                this.selectedItem = this.items[i];
                const src = this.items[i];
                const img = new Image();
                img.src = src;
                img.onload = () => {
                    this.loading = false;
                    this.src = src
                }
            },
            next() {
                this.show(this.index - 1);
            },
            prev() {
                this.show(this.index + 1);
            },
            close() {
                this.index = -1;
                this.events.forEach(e => {
                    window.removeEventListener(e[0], e[1]);
                });
                this.events = [];
            },
            bg(url) {
                const convertUrl = url;
                return convertUrl && convertUrl.length > 0
                    ? `background-image: url('${convertUrl}')`
                    : '';
            },
        },
    });
</script>

<style scoped>
    .fade-enter-active,
    .fade-leave-active {
        transition: opacity 1s ease;
    }

    .fade-enter-from,
    .fade-leave-to {
        opacity: 0;
    }
    .gi-container {
        width: 100%;
        height: 100%;
    }

    .gi-grid {
        position: relative;
        display: block;
        cursor: pointer;
        background: #000000;
        height: 100%;
    }

    .border-gi-grid .gi-grid {
        border-radius: 0 0 6px 6px;
    }

    .gi-item {
        position: absolute;
        background-position: center center;
        background-repeat: no-repeat;
        background-size: cover;
        border-top: solid 2px #fff;
        border-right: solid 2px #fff;
        overflow: hidden;
    }

    .gi-item:hover, .gi-item:focus {
    //opacity: 0.6;
    }

    .disable-hover {
        .gi-item:hover {
            opacity: 1;
        }

        .gi-item:focus {
            opacity: 1;
        }
    }


    .gi-grid-1 .gi-item {
        width: 100%;
        height: 100%;
    }

    .gi-grid-2 .gi-item, .gi-grid-3 .gi-item, .gi-grid-4 .gi-item, .gi-grid-5 .gi-item {
        width: 50%;
    }

    .gi-grid-2 .gi-item, .gi-grid-3 .gi-item:nth-child(1), .gi-grid-4 .gi-item:nth-child(1) {
        height: 100%;
    }

    .gi-grid-3 .gi-item:nth-child(2), .gi-grid-3 .gi-item:nth-child(3), .gi-grid-5 .gi-item:nth-child(1), .gi-grid-5 .gi-item:nth-child(2) {
        height: 50%;
    }

    .gi-item:last-child, .gi-grid-2 .gi-item:nth-child(2), .gi-grid-3 .gi-item:nth-child(2), .gi-grid-3 .gi-item:nth-child(3), .gi-grid-4 .gi-item:nth-child(2), .gi-grid-4 .gi-item:nth-child(3), .gi-grid-4 .gi-item:nth-child(4), .gi-grid-5 .gi-item:nth-child(3), .gi-grid-5 .gi-item:nth-child(4), .gi-grid-5 .gi-item:nth-child(5) {
        left: auto;
        right: 0;
        border-right: 0;
    }

    .gi-grid-3 .gi-item:nth-child(3), .gi-grid-4 .gi-item:nth-child(4), .gi-grid-5 .gi-item:nth-child(2), .gi-grid-5 .gi-item:nth-child(5) {
        bottom: 0;
        top: auto;
    }

    .gi-grid-4 .gi-item:nth-child(3), .gi-grid-5 .gi-item:nth-child(4) {
        top: 33.333333333333336%;
    }

    .gi-grid-4 .gi-item:nth-child(2), .gi-grid-4 .gi-item:nth-child(3), .gi-grid-4 .gi-item:nth-child(4), .gi-grid-5 .gi-item:nth-child(3), .gi-grid-5 .gi-item:nth-child(4), .gi-grid-5 .gi-item:nth-child(5) {
        height: 33.333333333333336%;
    }

    .gi-more {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        text-align: center;
        color: #fff;
        font-size: 3rem;
        background-color: rgba(0, 0, 0, .4);
    }

    .gi-more:before {
        display: inline-block;
        content: "";
        vertical-align: middle;
        height: 100%;
    }

    .gi-modal {
        transition: opacity .2s ease;
        position: fixed;
        z-index: 99999;
        top: 0;
        left: 0;
        bottom: 0;
        right: 0;
        width: 100%;
        min-height: 100%;
        height: 100vh;
        background-color: rgba(0, 0, 0, .8);
        display: block;
        -webkit-user-select: none;
        -khtml-user-select: none;
        -o-user-select: none;
        user-select: none;
    }

    .gi-modal-close {
        position: absolute;
        right: 20px;
        top: 20px;
        color: #fff;
    }

    .gi-modal-next, .gi-modal-prev {
        position: absolute;
        top: 50%;
        left: 20px;
        margin-top: -25px;
        z-index: 999;
        cursor: pointer;
        color: #fff;
        background-color: transparent;
        border: none;
    }

    .gi-modal-next {
        left: auto;
        right: 20px;
    }

    .gi-modal-img {
        position: absolute;
        top: 10px;
        left: 70px;
        right: 70px;
        bottom: 10px;
        text-align: center;
    }

    .gi-modal-img:before {
        display: inline-block;
        content: "";
        vertical-align: middle;
        height: 100%;
    }

    .gi-modal-img img {
        max-width: 100%;
        max-height: 100%;
        vertical-align: middle;
        object-fit: contain;
    }

    .gi-modal-img video {
        max-width: 100%;
        max-height: 100%;
        vertical-align: middle;
        object-fit: contain;
    }

    .gi-modal-img .spinner {
        width: 150px;
        position: absolute;
        margin-left: -75px;
        top: 50%;
        left: 50%;
        z-index: 1;
    }
    .gi-btn {
        padding: 9px 6px;
        transition: .25s;
        border-radius: 4px;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .gi-btn:hover {
        background: #5A8DEE;
    }
</style>
