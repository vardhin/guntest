<script>
import gun from "gun";

const Gun = gun({
    peers: ['https://gun-manhattan.herokuapp.com/gun', 'https://gunjs.herokuapp.com/gun']
});

let dbName = '';
let text = '';

Gun.on("ready", () => {
  console.log("Gun is ready")
});

const putData = (dbName, str) => {
  const db = Gun.get(dbName);
  db.put({
    data: str
  });
};

const getData = (dbName) => {
  return new Promise((resolve) => {
    const db = Gun.get(dbName);
    db.on((data) => {
      resolve(data);
    });
  });
};

</script>


<div>
  <form on:submit|preventDefault={() => putData(dbName, text)}>
    <div>
      <label for="dbName">Database Name:</label>
      <input type="text" id="dbName" bind:value={dbName} />
    </div>
    <div>
      <label for="text">Text:</label>
      <input type="text" id="text" bind:value={text} />
    </div>
    <button type="submit">Save Data</button>
  </form>
</div>

{#if dbName}
  {#await getData(dbName)}
    <p>Loading data...</p>
  {:then result}
    {#if result?.data}
      <div class="result">
        <h3>Data from "{dbName}":</h3>
        <p>{result.data}</p>
      </div>
    {:else}
      <p>No data found in "{dbName}"</p>
    {/if}
  {:catch error}
    <p class="error">Error loading data: {error}</p>
  {/await}
{/if}
