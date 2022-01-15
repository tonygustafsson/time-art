<script>
  import { onMount } from "svelte";

  const convertToHex = (str) => {
    let result = "";

    for (let i = 0; i < str.length; i++) {
      const hex = str.charCodeAt(i).toString(16);
      result += ("000" + hex).slice(-4);
    }

    return result;
  };

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
      currentTime = await sha256(`${new Date().toString()}`);
      timeParts = currentTime.match(/.{1,3}/g);
    };

    const interval = setInterval(createTimeParts, 2500);
    createTimeParts();

    return () => {
      clearInterval(interval);
    };
  });

  $: currentTime = "";
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
  }

  .grid > div {
    display: flex;
    width: calc(10vw - 4px);
    height: calc(10vw - 4px);
    margin: 2px;
    align-items: center;
    justify-content: center;
    transition: background-color 500ms;
    border-radius: 10%;
  }
</style>
