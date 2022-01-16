<script>
  import { onMount } from "svelte";

  const genRanHex = (size) =>
    [...Array(size)]
      .map(() => Math.floor(Math.random() * 16).toString(16))
      .join("");

  const sha256 = async (message) => {
    // encode as UTF-8
    const msgBuffer = new TextEncoder().encode(message);

    // hash the message
    const hashBuffer = await crypto.subtle.digest("SHA-512", msgBuffer);

    // convert ArrayBuffer to Array
    const hashArray = Array.from(new Uint8Array(hashBuffer));

    // convert bytes to hex string
    const hashHex = hashArray
      .map((b) => b.toString(16).padStart(2, "0"))
      .join("");

    return hashHex;
  };

  onMount(() => {
    const createTimeParts = async () => {
      const encryptedTime = await sha256(`${new Date().toString()}`);
      const encryptedTimeWithPadding = encryptedTime
        .split("")
        .map((char) => genRanHex(1) + char + genRanHex(1))
        .join("");
      const newTimeParts = encryptedTimeWithPadding.match(/.{1,3}/g);

      timeParts = newTimeParts;
    };

    const interval = setInterval(createTimeParts, 2500);
    createTimeParts();

    return () => {
      clearInterval(interval);
    };
  });

  $: timeParts = [];
</script>

<div class="grid">
  {#each timeParts as timeFragment}
    <div style="background-color: #{timeFragment}">
      #{timeFragment}
    </div>
  {/each}
</div>

<style>
  .grid {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-content: center;
    height: 100vh;
  }

  .grid > div {
    display: flex;
    width: calc(6vw - 4px);
    height: calc(6vw - 4px);
    margin: 2px;
    align-items: center;
    justify-content: center;
    transition: background-color 500ms;
    border-radius: 10%;
  }
</style>
