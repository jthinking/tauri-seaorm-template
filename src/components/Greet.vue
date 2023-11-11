<script setup lang="ts">
import { onMounted, ref } from "vue";
import { invoke } from "@tauri-apps/api/tauri";

interface Post {
  id: number;
  title: string;
  text: string;
}

interface FlashData {
  kind: string;
  message: string;
}

const greetMsg = ref("");
const name = ref("");
const posts = ref<Post[]>([]);

const createPost = async () => {
  const result = await invoke<FlashData>("create_post", {
    form: { title: name.value, text: "Hello! " + name.value },
  });
  greetMsg.value = result.message;
  await listPosts();
};

const listPosts = async () => {
  const result = await invoke<Post[]>("list_posts", {
    params: { page: 1, postsPerPage: 100 },
  });
  posts.value = result;
};

onMounted(() => {
  listPosts();
});
</script>

<template>
  <table>
    <thead>
      <tr>
        <th>Id</th>
        <th>Title</th>
        <th>Text</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="post in posts" :key="post.id">
        <td>{{ post.id }}</td>
        <td>{{ post.title }}</td>
        <td>{{ post.text }}</td>
      </tr>
    </tbody>
  </table>
  <form class="row" @submit.prevent="createPost">
    <input id="greet-input" v-model="name" placeholder="Enter a name..." />
    <button type="submit">Greet</button>
  </form>

  <p>{{ greetMsg }}</p>
</template>
<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  margin-bottom: 20px;
}

th,
td {
  padding: 0.25rem;
  border: 1px solid #ccc;
  text-align: left;
}
</style>
