<template>
  <h1>Todo 追加</h1>
  <div>
    <div>
      <p>Title:</p>
      <v-text-field
          v-model="title"
          id="title"
          required
      >
      </v-text-field>
    </div>
    <div>
      <p>Body:</p>
      <v-textarea
          v-model="body"
          id="body"
          required
      >
      </v-textarea>
    </div>
    <v-btn
        @click="submit"
        color="green"
    >
      Post
    </v-btn>
  </div>
</template>

<script setup>
const router = useRouter();
const title = ref();
const body = ref();

const submit = async () => {
  try {
    await useFetch('http://127.0.0.1:8000/api/todo',
        {
          method: 'POST',
          body: {
            title:title.value,
            body:body.value
          }
        });
    await router.push({path: "/"})
  } catch (error) {
    console.error('Error posting data', error);
  }
}
</script>