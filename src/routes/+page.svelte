<script>
  import Button from "../components/button.svelte";
  import Nonogram from "../components/nonogram.svelte";
  import Select from "../components/select.svelte";

  let nonogram = $state();
  let size = $state();
  let sizes = [
    {name: '5x5', value: 5},
    {name: '8x8', value: 8},
    {name: '10x10', value: 10}
  ]

  function randomNonogram() {
		nonogram.random(size);
	}
</script>

<div>
  <div class="w-fit mx-auto mt-4">
    <Nonogram bind:this={nonogram}></Nonogram>
  </div>
  <div class="fixed grid gap-2 bottom-4 right-4 !w-30">
    <Select options={sizes} placeholder="Select size" bind:select={size}></Select>
    <Button onclick={randomNonogram}>New</Button>
  </div>
</div>