<template>
	<div>
    <div class="filter">
      <span>Apply user filter</span>
      <button @click="applyUserPostFiler">Apply</button>
    </div>
		<h4 class="text-center">Posts</h4>
		<div v-for="post in posts" :key="post.id" class="post">
      <div>
        <span><b>Title:</b> {{ post.title }}</span>
        <span><b>Content:</b> {{ post.body }}</span>
      </div>
      <div>
        <button href="#" @click.stop="router.push(`edit/${post.id}`)">Edit</button>
        <button href="#" class="delete-button" @click="deletePost(post.id)">Delete</button>
      </div>
    </div>

    <paginationControl />
	</div>
</template>


<script>
import { useRouter } from 'vue-router';
import { ref, computed } from 'vue';
import { usePostStore } from '../stores/posts';
import { useUserStore } from '../stores/user';
import paginationControl from 'src/components/paginationControl.vue';

export default {
  components: { paginationControl },
  setup() {
    // initialization
    const router = useRouter();
    const postStore = usePostStore();
    const userStore = useUserStore();
    const posts = computed(() => postStore.getPaginatedPosts)

    // delete post
    const deletePost = (id) => {
      postStore.deletePost(id);
    }

    // apply filter
    const applyUserPostFiler = () => {
      userStore.setUserFilter()
      postStore.setPagination({ currentPage: 1, perPage: 10 }); //reset the pagination
    }
  
    return {
      posts,
      deletePost,
      applyUserPostFiler,
      router
    };
  },
};
</script>


<style scoped>
 .post {
  width: 900px;
  margin: 8px auto;
  border: solid 1px black;
  border-radius: .5rem;
  padding: .5rem;
 }

 .post span {
   display: block;
 }

 a {
  margin-left: 4px;
  color: blue;
 }

 .filter {
  position: absolute;
  top: 200px;
 }

 button {
  margin-left: 10px;
  background-color: yellowgreen;
  border: 0;
  border-radius: 0.5rem;
  cursor: pointer;
 }
 .delete-button {
  background-color: red;
 }
</style>

