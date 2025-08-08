<script>
  import Nonogram from "../components/nonogram.svelte";
  let nonogram = $state();
  let size = $state();
  let sizes = [
    {name: '5x5', value: 5},
    {name: '8x8', value: 8},
    {name: '10x10', value: 10}
  ]

  function randomNonogram() {
		nonogram.random(size)
	}
</script>

<div>
  <div class="w-fit mx-auto mt-4">
    <Nonogram bind:this={nonogram}></Nonogram>
  </div>
  <div class="fixed bottom-4 right-4">
    <select class="bg-black px-4 py-2" bind:value={size}>
      {#each sizes as size}
        <option value={size.value}>{size.name}</option>
      {/each}
    </select>
    <button class="bg-black px-4 py-2 cursor-pointer" onclick={randomNonogram}>New</button>
  </div>
</div>