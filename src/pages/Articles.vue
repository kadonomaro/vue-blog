<script>
    import { mapState } from "vuex";
    import gql from "graphql-tag";
    import { uniqueArray } from "@/utils";
    import { metaInfo } from "@/libs/MetaInfo";
    import CardList from "@/components/CardList";
    import PostFilter from "@/components/PostFilter";
    import SkeletonCard from "@/components/SkeletonCard";
    import CardListEmpty from "@/components/CardListEmpty";

    export default {
        name: "Articles",
        components: { CardListEmpty, SkeletonCard, PostFilter, CardList },
        computed: {
            ...mapState({
                filterTag: (state) => state.filter.tag,
                filterAuthor: (state) => state.filter.author,
            }),
            postsTags() {
                return this.posts?.flatMap((post) => post.tags).filter(uniqueArray);
            },
            postsAuthors() {
                return this.authors?.map((author) => author.name);
            },
            filteredPosts() {
                return this.posts
                    ? this.posts
                          .filter((post) => {
                              return this.filterTag === "all"
                                  ? post
                                  : post.tags.includes(this.filterTag);
                          })
                          .filter((post) => {
                              return this.filterAuthor === "all"
                                  ? post
                                  : post.author?.name === this.filterAuthor;
                          })
                    : [];
            },
        },
        apollo: {
            authors: gql`
                query {
                    authors {
                        name
                    }
                }
            `,
            posts: gql`
                query {
                    posts {
                        id
                        slug
                        title
                        announce
                        createdAt
                        tags
                        backgroundImageColor {
                            hex
                        }
                        previewImage {
                            url
                        }
                        author {
                            name
                            picture {
                                url
                            }
                        }
                    }
                }
            `,
        },
        metaInfo() {
            return metaInfo.get("articles");
        },
    };
</script>

<template>
    <div class="articles">
        <h1 class="title-main">Все статьи</h1>
        <div class="articles__inner">
            <aside class="articles__side">
                <div class="articles__filter">
                    <post-filter :tags="postsTags" :authors="postsAuthors"></post-filter>
                </div>
            </aside>
            <main class="articles__main">
                <skeleton-card v-if="$apollo.queries.posts.loading"></skeleton-card>
                <card-list v-if="filteredPosts.length" :cards="filteredPosts"></card-list>
                <card-list-empty
                    v-if="!$apollo.queries.posts.loading && !filteredPosts.length"
                ></card-list-empty>
            </main>
        </div>
    </div>
</template>

<style lang="scss">
    .articles {
        padding-top: 20px;
        @include bp($bp-mobile) {
            padding-top: 0;
        }
    }
    .articles__inner {
        @include bp($bp-desktop-sm) {
            display: flex;
        }
    }

    .articles__main {
        @include bp($bp-desktop-sm) {
            flex-grow: 1;
            margin-left: 32px;
        }
    }

    .articles__side {
        @include bp($bp-desktop-sm) {
            flex-basis: 300px;
            flex-shrink: 0;
            max-width: 300px;
        }
    }

    .articles__filter {
        margin-bottom: 20px;
        @include bp($bp-desktop-sm) {
            @include sticky(100px);
            margin-bottom: 0;
        }
    }
</style>
