<script>
  import {onMount} from "svelte";
  import * as toxicty from "@tensorflow-models/toxicity";

  let classification;
  let boot;
  let model;
  let buffer = "";

  onMount(() => (boot = toxicty.load(0.9).then((_model) => (model = _model))));

  function evaluate() {
    classification = model.classify([buffer]).then((result) => Math.round(result[6].results[0].probabilities[1] * 100));
  }
</script>

<main>
  {#await boot}
    <span>Loading model...</span>
  {:then}
    {#await classification}
      <span>Classifying...</span>
    {:then ammount}
      <form on:submit|preventDefault={evaluate}>
        <input type="text" bind:value={buffer} />
        <button>Search</button>
        {#if ammount}
          <div>Toxicity: {ammount}%</div>
        {/if}
      </form>
    {/await}
  {/await}
</main>
