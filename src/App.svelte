<script>
  import Dataio, {
    neo4jDataWrite,
    neo4jDataList,
    neo4jDataDelete,
    neo4jreplyWrite
  } from "./Dataio.svelte";

  import Replyio from "./Replyio.svelte";

  let title = "";
  let yourData = "";
  let waitForprocess = "";
  let userId = "";
  let posts = "";
  let searchWord = "";
  let replyData = "";

  async function dataWrite() {
    await neo4jDataWrite(userId, title, yourData);
    await dataRead(userId, searchWord);
    title = "";
    yourData = "";
  }

  async function dataRead(userId, searchWord) {
    posts = await neo4jDataList(userId, searchWord);
  }

  async function dataDelete(postId) {
    await neo4jDataDelete(postId);
    await dataRead(userId, searchWord);
  }

  async function replyWrite(postId) {
    await neo4jreplyWrite(replyData, postId);
    posts = "";
    await dataRead(userId, searchWord);

    replyData = "";
  }

  $: posts = dataRead(userId, searchWord);
</script>

<style>
  input {
    width: 30rem;
  }
</style>

<label for="userId">Enter Your ID</label>
<input type="text" bind:value={userId} />

<form on:submit|preventDefault={dataWrite}>
  <label for="title">Title</label>
  <input type="text" bind:value={title} />
  <label for="yourData">Fill Some</label>
  <textarea rows="10" bind:value={yourData} />
  <button type="submit">Save</button>
</form>

<label for="search">For Search</label>
<input type="text" bind:value={searchWord} />

{#if posts}
  {#await posts}
    <p>Wait for DB</p>
  {:then posts}
    {#each posts.records as post (post.get(0).properties.id)}
      <div style="margin:50px 0">
        <button on:click={dataDelete(post.get(0).properties.id)}>Delete</button>
        <h1>{post.get(0).properties.title}</h1>
        <h3>{post.get(0).properties.data}</h3>
        <Replyio postId={post.get(0).properties.id} />

        <input type="text" bind:value={replyData} />
        <button on:click={replyWrite(post.get(0).properties.id)}>
          Save reply
        </button>
      </div>
    {/each}
  {/await}
{/if}
