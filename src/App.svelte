<script>
  import { Configuration, OpenAIApi } from "openai";
  import Loader from "./lib/Loader.svelte";

  const openai_secret = "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";
  const configuration = new Configuration({
    apiKey: openai_secret,
  });
  const openai = new OpenAIApi(configuration);

  let images = [];
  let size = "";
  let prompt = "";
  let loading = false;

  async function handleSubmit() {
    try {
      loading = true;
      const response = await openai.createImage({
        prompt: prompt,
        n: 2,
        size: size,
      });

      console.log(response)
      images = [...images, ...response.data.data];
      prompt = "";
    } catch (error) {
      console.error(error);
    } finally {
      loading = false;
    }
  }
</script>

<main class="container">
  <h1>DALLE!</h1>
  <p>Image generator</p>

  <form class="create__form" on:submit|preventDefault={handleSubmit}>
    <input bind:value={prompt} type="text" placeholder="Enter Subject" />

    <select class="select" bind:value={size}>
      <option value="256x256">256x256</option>
      <option value="512x512">512x512</option>
      <option value="1024x1024">1024x1024</option>
    </select>

    <button type="submit">Create</button>
  </form>

  {#if loading}
    <div class="loader">
      <Loader />
    </div>
  {:else if images.length > 0}
    <div class="grid__image">
      {#each images as image}
        <img class="image__demo" src={image.url} />
      {/each}
    </div>
  {:else}
    <p>No images found.</p>
  {/if}
</main>
