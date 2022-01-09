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
      clearInterval(interval);
    }, 1000);

    return () => {
      clearInterval(interval);
    };
  });

  $: currentTime = new Date().toString();
</script>

<main>
  <h1>Hello {name}!</h1>
  <p>
    Visit the <a href="https://svelte.dev/tutorial">Svelte tutorial</a> to learn
    how to build Svelte apps.
  </p>

  <p class="hex">{currentTime}</p>
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 640px;
    margin: 0 auto;
  }

  h1 {
    color: #ff3e00;
    text-transform: uppercase;
    font-size: 4em;
    font-weight: 100;
  }
  .hex {
    overflow-wrap: anywhere;
  }
</style>
