<script>
  import { onMount } from "svelte";

  export let name;

  const convertToHex = (str) => {
    let result = "";

    for (let i = 0; i < str.length; i++) {
      const hex = str.charCodeAt(i).toString(16);
      result += ("000" + hex).slice(-4);
    }

    return result;
  };

  async function sha256(message) {
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
  }

  onMount(() => {
    const interval = setInterval(async () => {
      currentTime = await sha256(`${new Date().toString()}`);
      timeParts = currentTime.match(/.{1,3}/g);
    }, 1000);

    return () => {
      clearInterval(interval);
    };
  });

  $: currentTime = "";
  $: timeParts = [];
</script>

<main>
  <p class="hex">{currentTime}</p>
  <div class="grid">
    {#each timeParts as timeFragment}
      <div class="time-block" style="background-color: #{timeFragment}">
        {timeFragment}
      </div>
    {/each}
  </div>
</main>

<style>
  .grid {
    display: flex;
    flex-wrap: wrap;
  }

  .time-block {
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 1000ms;
  }

  .grid > div {
    width: 4vw;
    height: 4vw;
  }
  .hex {
    overflow-wrap: anywhere;
  }
</style>
