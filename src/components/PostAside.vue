<script>
    import Swiper from "swiper/swiper-bundle.min";
    import CardListItem from "@/components/CardListItem";

    export default {
        name: "PostAside",
        components: {
            CardListItem,
        },
        props: {
            posts: {
                type: Array,
                required: true,
            },
            title: String,
        },
        data() {
            return {
                slider: null,
            };
        },
        mounted() {
            this.$nextTick(() => {
                this.slider = new Swiper(".js-cards-slider", {
                    spaceBetween: 20,
                    direction: "horizontal",
                    grabCursor: true,
                    mousewheel: {
                        forceToAxis: true,
                    },
                    pagination: {
                        el: ".swiper-pagination",
                        type: "progressbar",
                    },
                });
            });
        },
    };
</script>

<template>
    <div class="post-aside">
        <h2 class="post-aside__title">{{ title }}</h2>
        <div class="post-aside__slider">
            <div class="swiper-container js-cards-slider">
                <div class="swiper-wrapper">
                    <div
                        class="swiper-slide post-aside__slide"
                        v-for="post in posts"
                        :key="post.id"
                    >
                        <card-list-item :card="post" is-vertical></card-list-item>
                    </div>
                </div>
            </div>
            <div v-if="posts.length > 1" class="post-aside__slider-pagination">
                <div class="swiper-pagination"></div>
            </div>
        </div>
    </div>
</template>

<style lang="scss">
    .post-aside {
        @include sticky(100px);
        padding: 16px;
        background-color: #fff;
        border-radius: 16px;
        border: 2px solid $color-border;
        box-sizing: border-box;
        overflow: hidden;
        .card-list__item {
            max-width: 100%;
            padding: 0 0 16px;
        }
    }

    .post-aside__title {
        margin: 0 0 10px;
        font-size: 24px;
    }

    .post-aside__slider {
        position: relative;
        .swiper-container {
            overflow: visible;
        }
        .swiper-pagination {
            width: 100%;
            height: 3px;
            border-radius: 3px;
            overflow: hidden;
        }
        .swiper-slide {
            height: auto;
        }
        .swiper-pagination-progressbar {
            background-color: $color-border;
            .swiper-pagination-progressbar-fill {
                background-color: $color-link;
            }
        }
    }

    .post-aside__slider-pagination {
        padding-top: 15px;
    }
</style>
