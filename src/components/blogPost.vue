<template>
    <div>
      <h4 class="text-center">Create a New Post</h4>
      <form @submit.prevent="submitPost">
        <div class="form-field">
          <label for="title">Title:</label>
          <input
            type="text"
            id="title"
            v-model="post.title"
            :class="{ 'is-invalid': titleError }"
          />
        </div>
        <div class="invalid-feedback text-left" v-if="titleError">{{ titleError }}</div>
    
        <div class="form-field">
          <label for="content">Content:</label>
          <textarea
            id="content"
            v-model="post.content"
            :class="{ 'is-invalid': contentError }"
          ></textarea>
        </div>
        <div class="invalid-feedback text-left" v-if="contentError">{{ contentError }}</div>
    
        <button type="submit" class="submit">Submit</button>
        <h6 v-show="successMessage.length > 0" class="text-left">{{ successMessage }}</h6>
      </form>

    </div>
  </template>
  
  
  <script>
  import { ref, computed } from 'vue';
  import { usePostStore } from '../stores/posts';
  import { useUserStore } from '../stores/user';
  import { useRouter } from 'vue-router';

  export default {
    setup() {
      // initialization
      const postStore = usePostStore();
      const userStore = useUserStore();
      const router = useRouter();
      const totalPosts = computed(() => postStore.posts.length);
      const perPage = computed(() => postStore.perPage);
      const fragment = window.location.hash;
      const parts = fragment.split('/');
      const id = parts[parts.length - 1];
      const titleError = ref('');
      const contentError = ref('');
      const post = ref({title: '', content: '', id: 0});
      const successMessage = ref('');
      const hasErrors = computed(() => titleError.value || contentError.value);

      // find post   
      if (parts.includes("edit")) {
        let editPost = postStore.fetchPostById(parseInt(id, 10));
        post.value.id = editPost.id;
        post.value.title  = editPost.title;
        post.value.content = editPost.body;
      }

      //validations
      const validateTitle = () => {
        if (post.value.title.trim() === '') {
          return 'Title is required.';
        }
        return '';
      };
  
      const validateContent = () => {
        if (post.value.content.trim() === '') {
          return 'Content is required.';
        }
        return '';
      };
  
      // upsert post
      const submitPost = () => {
        // Validate fields before submission
        titleError.value = validateTitle();
        contentError.value = validateContent();


        if (!hasErrors.value) {

          if (post.value.id == 0) {
            postStore.createPost({
              "userId": userStore.getCurrentUser.id,
              "id": totalPosts.value + 1,
              "title": post.value.title,
              "body": post.value.content
            });

            successMessage.value = "Successfully created";
          } else {
            postStore.updatePost({
              "userId": userStore.getCurrentUser.id,
              "id": post.value.id,
              "title": post.value.title,
              "body": post.value.content
            });

            successMessage.value = "Successfully updated";
          }
          
          // Clear the form after submission
          post.value.title = '';
          post.value.content = '';
          titleError.value = '';
          contentError.value = '';
          
          setTimeout(() => {
            successMessage.value = "";
            router.push("/");
          }, 500);
        }
      };
  
      return {
        post,
        titleError,
        contentError,
        hasErrors,
        successMessage,
        submitPost,
      };
    },
  };
  </script>
  
  <style scoped>
    form {
      display: flex;
      flex-direction: column;
      justify-content: center;
      width: 400px;
      margin: auto;
    }
  
    .form-field {
      width: 100%;
      display: flex;
      align-items: center;
      margin-top: 8px;
    }
  
    button {
      background-color: green;
      color: white;
      border: 0;
      border-radius: .5rem;
      width: 200px;
      margin: 8px auto;
    }
  
    label {
      width: 80px;
    }
  
    input, textarea {
      width: 320px;
    }
  
    .is-invalid {
      border-color: red;
    }
    .invalid-feedback {
      color: red;
      font-size: 0.8rem;
      margin-top: 0.2rem;
      width: 320px;
      margin-left: 80px;
    }

    h6 {
      color: green;
      font-size: 14px;
      margin: 10px 80px;
    }
  </style>
  
  