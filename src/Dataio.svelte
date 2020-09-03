<script context="module">
  import neo4j from "../node_modules/neo4j-driver/lib/browser/neo4j-web.js";

  const uri = "bolt://localhost:7687";
  const user = "nabi";
  const password = "1212";
  const driver = neo4j.driver(uri, neo4j.auth.basic(user, password));
  const session = driver.session();

  export async function neo4jDataWrite(userId, title, yourData) {
    const random = parseInt(Math.random() * 1000);
    const id = userId + random;
    const result = await session.run(
      "MATCH (user:user) WHERE user.ID = $userId CREATE (post:post {id:$postId, title: $title, data:$yourData}), (user)-[:write]->(post) RETURN post",
      {
        title: title,
        yourData: yourData,
        postId: id,
        userId: userId
      }
    );
    // const singleRecord = await result.records[0];
    // const node = await singleRecord.get(0);

    // await session.close();
    return result;
  }
  export async function neo4jDataList(userId, searchWord) {
    const posts = await session.run(
      "MATCH (user:user)-[:write]-(posts:post) WHERE user.ID=$userId AND (posts.data contains $searchWord OR posts.title contains $searchWord) return posts",
      {
        userId: userId,
        searchWord: searchWord
      }
    );
    console.log(posts);
    return posts;
  }
  export async function neo4jDataDelete(postId) {
    await session.run(
      "MATCH (post:post)-[r]-() WHERE post.id=$postId delete r, post",
      {
        postId: postId
      }
    );
  }
  export async function neo4jreplyWrite(replyData, postId) {
    const random = parseInt(Math.random() * 1000);
    const id = postId + random;
    await session.run(
      "MATCH(post:post) WHERE post.id =$postId CREATE(reply:reply{id:$id, data:$replyData})-[:reple]->(post)",
      {
        id: id,
        replyData: replyData,
        postId: postId
      }
    );
  }

  // on application exit:
  // driver.close();
</script>
