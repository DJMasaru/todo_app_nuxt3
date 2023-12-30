<template>
  <h1>Todo 一覧</h1>
  <v-table>
    <thead>
    <tr>
      <th class="text-left">
        id番号
      </th>
      <th class="text-left">
        タイトル
      </th>
      <th class="text-left">
        本文
      </th>
      <th class="text-left">
        編集ボタン
      </th>
      <th class="text-left">
        削除ボタン
      </th>
    </tr>
    </thead>
    <tbody>
    <tr
        v-for="todo in todos.value"
        :key="todo.id"
    >
      <td>{{ todo.id }}</td>
      <td>{{ todo.title }}</td>
      <td>{{ todo.body }}</td>
      <td><v-btn @click="modifyTodoDialog(todo.id)" color="blue">編集</v-btn></td>
      <td><v-btn @click="deleteTodoDialog(todo.id)" color="red">削除</v-btn></td>
    </tr>
    </tbody>
  </v-table>
  <v-dialog
      v-model="modifyDialog"
      max-width="290"
  >
    <v-card>
      <v-card-title class="text-h5">
        Todo編集画面
      </v-card-title>

      <v-card-text>
        値を入力し、操作を行なってください。
      </v-card-text>
      <label for="title">Title:</label>
      <v-text-field
          v-model="modifyTitle"
          type="text"
          id="title"
          required
      >
      </v-text-field>
      <label for="body">Body:</label>
      <v-textarea
          v-model="modifyBody"
          type="text"
          id="body"
          required
      >
      </v-textarea>
      <v-card-actions>
      <!--これはただの改行用のソースコード-->
        <v-spacer></v-spacer>
        <v-btn
            color="green darken-1"
            @click=modifyTodo
        >
          更新する
        </v-btn>
        <v-btn
            color="green darken-1"
            @click="modifyDialog = false"
        >
          やめる
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
  <v-dialog
      v-model="deleteDialog"
      max-width="290"
  >
    <v-card>
      <v-card-title class="text-h5">
        Todo削除確認画面
      </v-card-title>
      <v-card-text>
        本当に消してもいいですか？
      </v-card-text>
      <v-card-actions>
        <!--これはただの改行用のソースコード-->
        <v-spacer></v-spacer>
        <v-btn
            color="green darken-1"
            @click=deleteTodo
        >
          削除する
        </v-btn>
        <v-btn
            color="green darken-1"
            @click="deleteDialog = false"
        >
          やめる
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
<script setup lang="ts">
  const modifyDialog =ref(false)
  const deleteDialog =ref(false)
  const modifyTitle = ref()
  const modifyBody = ref()
  const todos = ref();
  const deleteId = ref<number | null>(null);
  const modifyId = ref<number | null>(null);
  const modifyTodoDialog = (id: number) => {
    modifyTodoInfo(id)
    modifyId.value = id;
    modifyDialog.value = true;
  };

  const deleteTodoDialog = (id: number) => {
    deleteId.value = id;
    deleteDialog.value = true;
  };

  //初回レンダリング時にデータを取得する
  const response = await useFetch('http://127.0.0.1:8000/api/todo');
  todos.value = response.data

  // 取得したデータの型指定
  interface TodoResponse {
    data: {
      value: {
        id: number;
        title: string;
        body: string;
      };
    };
  }
  const modifyTodoInfo =async (id: number)=>{
    try {
      const response = await useFetch('http://127.0.0.1:8000/api/todo/individual',
          {
            method: 'GET',
            params: {
              id: id,
            }
          }) as TodoResponse;
      modifyTitle.value = response.data.value.title;
      modifyBody.value = response.data.value.body;
    } catch (error) {
      console.error('Error fetching modify data', error);
    }
  }

  const modifyTodo=async () => {
    try {
      await useFetch('http://127.0.0.1:8000/api/todo',
          {
            method: 'PUT',
            body: {
              id: modifyId.value,
              title: modifyTitle.value,
              body: modifyBody.value,
            }
          });
      modifyDialog.value = false
    } catch (error) {
      console.error('Error putting data', error);
    } finally {
      const response = await useFetch('http://127.0.0.1:8000/api/todo');
      todos.value = response.data
    }
  }

  const deleteTodo =async () => {
    try {
      await useFetch('http://127.0.0.1:8000/api/todo',
          {
            method: 'DELETE',
            body: {
              id: deleteId.value,
            }
          });
      deleteDialog.value = false
    } catch (error) {
      console.error('Error deleting data', error);
    } finally {
      const response = await useFetch('http://127.0.0.1:8000/api/todo');
      todos.value = response.data
    }
  }
</script>