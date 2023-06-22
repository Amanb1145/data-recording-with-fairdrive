<script lang="ts">
  import { newTodo, user, savedMnemonic, todoItems, wallet } from "../store";
  import type { Todo } from "../types";
  import { addTodo, ago, deleteTodo, ENTER_KEY, saveMnemonic } from "../utils";
  import Register from "./Register.svelte";

  const onKeyDown = (e) => {
    const id = new Date().getTime();
    const todo: Todo = {
      id,
      text: "",
      file: null,
      done: false,
      createdAt: id,
    };
    $newTodo = "";
    addTodo(todo, $todoItems).then((todos) => {
      $todoItems = todos;
    });
    // if (e.keyCode == ENTER_KEY) {
    //   const id = new Date().getTime();
    //   const todo: Todo = {
    //     id,
    //     text: "",
    //     file: null,
    //     done: false,
    //     createdAt: id,
    //   };
    //   $newTodo = "";
    //   addTodo(todo, $todoItems).then((todos) => {
    //     $todoItems = todos;
    //   });
    // }
  };
  function onFileChange(event: MouseEvent) {
    const id = new Date().getTime();
    const todo: Todo = {
      id,
      text: "",
      file: null,
      done: false,
      createdAt: id,
    };
    $newTodo = "";
    addTodo(todo, $todoItems).then((todos) => {
      $todoItems = todos;
    })
}
async function urlGetter() {
  const url = await URL.createObjectURL($todoItems[0].blob);
  console.log(url, "mmmmmmmmmmmmmmmmmmmmmmmm")
  return url
}
  // function onFileChange(event) {
  //   const id = new Date().getTime();
  //   const todo: Todo = {
  //     id,
  //     text: "",
  //     file: event.target.files[0],
  //     done: false,
  //     createdAt: id,
  //   };
  //   $newTodo = "";
  //   addTodo(todo, $todoItems).then((todos) => {
  //     $todoItems = todos;
  //   });
  // };
  console.log("00000");
  let videoUrl;
  let error;

  async function loadVideoUrl(todo) {
    try {
      const url = await URL.createObjectURL(todo.blob);
      videoUrl = url;
    } catch (err) {
      error = err;
    }
  }

  function downloadFile(todo) {
    const url = URL.createObjectURL(todo.blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = todo.name;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
    URL.revokeObjectURL(url);
  }
</script>

{#if $wallet?.mnemonic && !$savedMnemonic}
  <dl class="notice">
    <dt>Wallet Address</dt>
    <dd>{$wallet.address}</dd>
    <dt>Wallet Mnemonic Phrase</dt>
    <dd>
      <pre>{$wallet?.mnemonic}</pre>
      <button on:click={() => saveMnemonic($wallet.mnemonic)}
        >Save Mnemonic Phrase</button
      >
    </dd>
  </dl>
{/if}
{#if !$user}
  <div class="notice">
    <p>
      <strong>Wallet: </strong><code>{$wallet.address}</code>
    </p>
    <Register />
  </div>
{/if}
<div class="notice">
  <p>
    <strong>Files ({$todoItems.length})</strong>
  </p>
  <input
    id="todo"
    on:keydown={onKeyDown}
    type="file"
    bind:value={$newTodo}
    placeholder="What needs to be done?"
  />
  <button
    on:click={onFileChange}
    id="upload"
    style="background: green; color: white;">Upload File</button
  >
  <!-- <video src={urlGetter}></video> -->
  {#if $todoItems.length}
    <table>
      <tr>
        <th>#</th>
        <th>type</th>
        <th>files</th>
        <th>name</th>
        <!-- <th>done</th> -->
        <!-- <th>created</th> -->
        <th>Delete</th>
        <th>download</th>

      </tr>
      {#each $todoItems as todo, idx (idx)}
        <tr>
          <td>{idx + 1}</td>
          <td>{todo.blob.type}</td>
          <!-- <td><img src={URL.createObjectURL(todo)} alt=""></td> -->
          <!-- <td><video src={URL.createObjectURL(todo)} alt=""></video></td> -->
          <td>
            {#if todo.blob.type === "video/webm" || todo.blob.type === "video/mp4"}
              {#await loadVideoUrl(todo)}
                <video src={videoUrl} alt="" autoplay></video>
              {:then}
                <video src={videoUrl} />
              {:catch error}
                <p>Error loading video</p>
              {/await}
              {:else if todo.blob.type === "image/png" || todo.blob.type == "image/jpeg"}
              <img src={URL.createObjectURL(todo.blob)} alt="" />
            {:else}
              {todo.blob.type}
            {/if}
          </td>
          
          <td>{todo.name}</td>
          <!-- <td
            ><input
              type="checkbox"
              disabled
              on:change={async () => {
                // $todoItems = await updateTodo(todo, $fdp, $todoItems);
              }}
              bind:checked={todo.done}
            /></td
          > -->
          <!-- <td>{ago(todo.createdAt)}</td> -->
          <td
            ><button
              on:click={async () => {
                $todoItems = await deleteTodo(todo.name, $todoItems);
              }}>Delete</button
            ></td
          >
          <td>
            <button on:click={() => downloadFile(todo)}>Download</button>
          </td>
        </tr>
      {/each}
    </table>
  {:else}
    Todo List is empty
  {/if}
</div>

<style>
  #todo {
    width: 100%;
    padding: 16px;
    border: none;
    background: lightyellow;
    box-shadow: inset 0 -2px 1px rgb(0 0 0 / 10%);
    margin-bottom: 2rem;
  }
  table {
    margin-top: 2rem;
  }
</style>
