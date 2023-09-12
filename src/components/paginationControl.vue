<template>
    <div class="flex items-center justify-center">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span> Page {{ currentPage }} </span>
      <button @click="nextPage" :disabled="currentPage * perPage >= totalPosts">Next</button>
    </div>
</template>

<script>
import { computed } from 'vue';
import { usePostStore } from '../stores/posts';
import { useUserStore } from '../stores/user';

export default {
  name: 'paginationControl',
  setup() {
    // constants
    const postStore = usePostStore()
    const userStore = useUserStore()
    const currentPage = computed(() => postStore.currentPage);
    const perPage = computed(() => postStore.perPage);
    const totalPosts = computed(() =>  postStore.getPosts.length);

    const prevPage = () => {
      if (currentPage.value > 1) {
        postStore.setPagination({ currentPage: currentPage.value - 1, perPage: perPage.value });
      }
    };

    const nextPage = () => {
      const maxPage = Math.ceil(totalPosts.value / perPage.value);
      if (currentPage.value < maxPage) {
        postStore.setPagination({ currentPage: currentPage.value + 1, perPage: perPage.value });
      }
    };

    return {
      currentPage,
      perPage,
      totalPosts,
      prevPage,
      nextPage,
    };
  },
};
</script>


<style scoped>
 div { 
  margin-top: 10px;
  margin-bottom: 10px;
 }
</style>