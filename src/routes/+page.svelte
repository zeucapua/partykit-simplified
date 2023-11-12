<script lang="ts">
  import { onMount } from "svelte";
  import PartySocket from "partysocket";

  let socket : PartySocket;

  let username : string;
  let message : string;

  let chat_history : string[] = [];

  function sendToPartyServer() {
    if (socket) {
      const parcel = JSON.stringify({
        message,
        username 
      });

      socket.send(parcel);
    }
  }

  onMount(() => {
    socket = new PartySocket({
      host: "localhost:1999",
      room: "room-id"
    });

    // listen to the server's broadcasts (this.party.broadcast)
    socket.addEventListener("message", (event) => {
      const envelope : { content: string }= JSON.parse(event.data);
      console.log(envelope);

      chat_history = [...chat_history, envelope.content];
    });
  });
</script>

<h1>PartyKit: Tech Simplified</h1>

<section id="user_controls">
  <label>
    Username:
    <input type="text" bind:value={username} />
  </label>

  <label>
    Chat:
    <input type="text" bind:value={message} />
  </label>

  <button on:click={sendToPartyServer}>Send</button>
</section>

<section id="history">
  {#each chat_history as chat}
    <p>{chat}</p>
  {/each}
</section>
