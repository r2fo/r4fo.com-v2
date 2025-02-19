<template>
  <div class="article-list">
    <noscript>
      <small>To use the filter you need JavaScript to be enabled.</small>
    </noscript>
    <div class="filter-tags">
      <button @click="filterPosts('')" id="all-tags">All</button>
      <button v-for="tag in uniqueTags" :key="tag" @click="filterPosts(tag)">
        <span class="hashtag">#</span>{{ tag }}
      </button>
    </div>
    <div class="post-container">
      <article v-for="post in filteredPosts" :key="post.title" class="post">
        <h3>
          <a
            :href="`posts/${post.title
              .toLowerCase()
              .replace(/\s+/g, '-')}.html`"
            >{{ post.title }}</a
          >
        </h3>
        <p class="date">{{ post.date }}</p>
        <p>{{ post.description }}</p>
        <div class="tags">
          <span v-if="typeof post.tags === 'string'" :key="post.tags"
            >#{{ post.tags }}</span
          >
          <span v-else v-for="tag in post.tags" :key="tag">#{{ tag }}</span>
        </div>
      </article>
    </div>
  </div>
</template>

<script setup lang="ts">
import "aplos/custom/ArticleList.scss";
import { data as posts } from "./posts.data";
import { computed, ref } from "vue";

const selectedTag = ref(null);

const allTags = computed(() => {
  return posts.reduce((tags, post) => {
    return tags.concat(Array.isArray(post.tags) ? post.tags : [post.tags]);
  }, []);
});

const uniqueTags = computed(() => {
  const tags = [...new Set(allTags.value)];
  return tags.filter((tag) => tag !== "");
});

const filteredPosts = computed(() => {
  return selectedTag.value === null
    ? posts
    : posts.filter((post) =>
        Array.isArray(post.tags)
          ? post.tags.includes(selectedTag.value)
          : post.tags === selectedTag.value,
      );
});

function filterPosts(tag: string) {
  selectedTag.value = tag === "" ? null : tag;
}
</script>
