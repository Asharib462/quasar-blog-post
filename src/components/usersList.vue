<template>
  <vSelect
    v-model="selectedUser"
    :options="users"
    label="name"
  ></vSelect>
</template>

<script>
import { useUserStore } from '../stores/user';
import { ref, watch } from 'vue';
import vSelect from "vue-select";
import "vue-select/dist/vue-select.css";
import { usePostStore } from '../stores/posts';

export default {
  name: "userList",
  components: {
    vSelect
  },
  setup() {
    const userStore = useUserStore();
    const postStore = usePostStore();
    const users = userStore.allUsers;
    const selectedUser = ref({});
    selectedUser.value =  userStore.getCurrentUser;

    watch(selectedUser, () => {
      userStore.setCurrentUser(selectedUser);
    });
  
    return {
      users,
      selectedUser,
    };
  },
}

</script>

<style>
.vs__dropdown-menu, .vs__selected {
  background-color: #f0f0f0; /* Set your desired background color here */
  color: #333; /* Set your desired text color here */
}

.v-select {
  width: 250px;
  position: absolute;
  top: 100px;
}
</style>