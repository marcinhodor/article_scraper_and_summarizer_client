<script>
  import { Circle } from "svelte-loading-spinners"
  import { fade } from "svelte/transition"
  export let article

  let summary = ""
  let error = null
  let loading = false

  const authors_formatted = (authors) => {
    let text = ""
    for (const author of authors) {
      if (author === authors.at(-1)) {
        text = text + author
      } else {
        text = text + author + ", "
      }
    }
    return text
  }

  const formatDotSpace = (text) => {
    return text.replace(/ \./g, ".")
  }

  const downloadSummary = async (text) => {
    loading = true
    const url = `https://articlesummarize-1-z7868205.deta.app/summarize`
    const response = await fetch(url, {
      method: "POST",
      mode: "cors",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        text,
      }),
    })
    const data = await response.json()
    if (response.status !== 200) {
      error = data.detail
      loading = false
      return
    }
    summary = formatDotSpace(data.data[0].summary_text)
    loading = false
  }
</script>

<!-- svelte-ignore a11y-no-static-element-interactions -->
<!-- svelte-ignore a11y-click-events-have-key-events -->
<div class="card">
  <div class="left">
    <img src={article.image} alt="article" />
  </div>
  <div class="middle">
    <h1 class="title">{article.title}</h1>
    <p class="authors">{authors_formatted(article.authors)}</p>
    {#if loading}
      <div class="spinner" in:fade>
        <Circle size="40" color="var(--dark-blue)" unit="px" duration="1s" />
      </div>
    {:else}
      <p class={`summary ${error ? "error" : ""}`}>
        {error || summary}
      </p>
    {/if}
  </div>
  <div class="right">
    <button class="button-top" on:click={downloadSummary(article.text)}
      >Summary</button
    >
    <a
      href={article.link}
      target="_blank"
      rel="noopener noreferrer"
      on:click|stopPropagation={""}
      ><button class="button-bottom">Full article</button></a
    >
  </div>
</div>

<style lang="scss">
  .card {
    display: grid;
    grid-template-columns: 160px minmax(0, 800px) 120px;
    border: 1px solid rgba(0, 0, 0, 0.2);
    margin: 8px;
    border-radius: 5px;
    box-shadow: 0 2px 3px 0 rgba(0, 0, 0, 0.2);
    padding: 8px;
    background-color: var(--white);

    // &:hover {
    //   background-color: #fafafa;
    // }

    .left {
      display: flex;
      justify-content: center;
      align-items: center;
      margin-right: 8px;

      img {
        max-width: 150px;
        // max-height: 100px;
        border-radius: 4px;
      }
    }

    .middle {
      display: flex;
      flex-direction: column;
      justify-content: center;
      width: 100%;

      .title {
        font-size: 1.2rem;
        color: var(--almost-black);
      }

      .authors {
        margin: 5px 0;
        color: #666666;
        font-size: 0.8rem;
      }
      .spinner {
        display: flex;
        justify-content: center;
      }

      .summary {
        font-style: italic;
        color: var(--almost-black);
      }

      .error {
        color: red;
      }
    }

    .right {
      display: flex;
      flex-direction: column;
      align-items: end;
      justify-content: center;
      margin-left: 8px;

      .button-top {
        margin-bottom: 8px;
        color: var(--dark-blue);
        background-color: var(--white);
        border: 1px solid var(--dark-blue);

        &:hover {
          background-color: var(--light-grey);
        }
      }

      .button-bottom {
        background-color: var(--dark-blue);

        &:hover {
          background-color: var(--dark-blue-hover);
        }
      }

      button {
        border: none;
        color: #fff;
        padding: 10px 12px;
        text-align: center;
        text-decoration: none;
        font-size: 14px;
        width: 100px;
        border-radius: 4px;
        z-index: 1;

        &:hover {
          cursor: pointer;
        }
      }
    }
  }

  @media (max-width: 600px) {
    .card {
      grid-template-columns: auto auto;
      grid-template-rows: auto auto;

      .left {
        display: flex;
        order: 1;
        margin: 0;
        justify-content: left;
      }

      .middle {
        order: 3;
        grid-column: span 2;
        margin-top: 0.8rem;

        .title {
          font-size: 1.1rem;
        }

        .summary {
          font-size: 0.9rem;
        }
      }

      .right {
        display: flex;
        order: 2;
        align-items: end;
        margin: 0;
      }
    }
  }
</style>
