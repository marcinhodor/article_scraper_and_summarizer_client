<script>
  import { onMount } from "svelte"
  import { fade } from "svelte/transition"
  import websites from "../content/websites/websites.json"

  // import data_file from "../data/dev_data.json"
  import ArticleCard from "./ArticleCard.svelte"
  import { Circle } from "svelte-loading-spinners"

  export let endpoint

  let articles = []
  let loading = false

  const imgAndTitle = (endpoint) => {
    const index = websites.findIndex(
      (website) => "/" + website.short_name === endpoint
    )
    // console.log(websites[0].short_name)
    const websiteInfo = websites[index]
    // console.log(websiteInfo)
    return websiteInfo
  }

  onMount(async function () {
    loading = true
    const url = `https://articlesummarize-1-z7868205.deta.app/articles${endpoint}`
    const response = await fetch(url)
    const data = await response.json()
    articles = data
    // articles = data_file
    loading = false
  })
</script>

<div class="articles">
  <div class="website-img">
    <img src={imgAndTitle(endpoint).image_url} alt="news website" />
  </div>
  {#if loading}
    <div class="spinner" in:fade>
      <Circle size="80" color="var(--dark-blue)" unit="px" duration="1s" />
      <p>Please give me a moment. I'm fetching the articles...</p>
    </div>
  {:else}
    <div in:fade>
      {#each articles as article}
        <ArticleCard {article} />
      {/each}
    </div>
  {/if}
</div>

<style lang="scss">
  .website-img {
    display: flex;
    margin-top: 8px;
    justify-content: center;
    img {
      max-width: max(150px, 20%);
      border-radius: 5px;
    }
  }

  .articles {
    align-self: center;
    width: min(1114px, 100%);
    // border: 1px solid red;

    .spinner {
      display: flex;
      flex-direction: column;
      align-items: center;
      margin-top: min(200px, calc(0.2 * 100vw));

      p {
        margin-top: 2rem;
        color: var(--almost-black);
      }
    }
  }
</style>
