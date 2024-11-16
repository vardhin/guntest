<script>
import gun from "gun";

const Gun = gun({
    peers: ['https://gun-manhattan.herokuapp.com/gun', 'https://gunjs.herokuapp.com/gun']
});

let dbName = '';
let text = '';
let retrievedData = '';

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
  const db = Gun.get(dbName);
  db.on((data) => {
    retrievedData = data?.data || '';
  });
};

const handleSubmit = async () => {
  putData(dbName, text);
  getData(dbName);
};

const handleFetch = () => {
  getData(dbName);
};

</script>


<div>
  <form on:submit|preventDefault={handleSubmit}>
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

  <button on:click={handleFetch} class="mt-4">Fetch Latest Data</button>

  {#if retrievedData}
    <div class="mt-4">
      <h3>Retrieved Data:</h3>
      <p>{retrievedData}</p>
    </div>
  {/if}
</div>

