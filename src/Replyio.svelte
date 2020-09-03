<script>
  import neo4j from "../node_modules/neo4j-driver/lib/browser/neo4j-web.js";

  const uri = "bolt://localhost:7687";
  const user = "nabi";
  const password = "1212";
  const driver = neo4j.driver(uri, neo4j.auth.basic(user, password));
  const session = driver.session();
  export let postId;
  let reply = [];

  async function neo4jreplyRead(postId) {
    const reply = await session.run(
      "MATCH (reply:reply)-[:reple]->(post:post) WHERE post.id=$postId return reply",
      {
        postId: postId
      }
    );

    return reply;
  }
  reply = neo4jreplyRead(postId);
</script>

{#if reply}
  {#await reply}
    <p>Wait for replyDB</p>
  {:then reply}
    {#each reply.records as replys}
      <h5>{replys.get(0).properties.data}</h5>
    {/each}
  {/await}
{/if}
