<template>
  <h1>Todo追加 Page</h1>
  <div style="display: flex">
    <label for="title">Title:</label>
    <input v-model="request.title" type="text" id="title" required>
    <label for="body">Body:</label>
    <textarea v-model="request.body" id="body" required></textarea>
    <button @click="submit">Post</button>
  </div>
</template>

<script setup>
const router = useRouter();
const request = ref({
  title: '',
  body: ''
})

const submit = async () => {
  try {
    const response = await useFetch('http://127.0.0.1:8000/api/todo',
        {
          method: 'POST',
          body: {
            title:request.value.title,
            body:request.value.body
          }
        });
    await router.push({path: "/"})
    console.log('Post successful', response.data.value);
  } catch (error) {
    console.error('Error posting data', error);
  }
}
</script>