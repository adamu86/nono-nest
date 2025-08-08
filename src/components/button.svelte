<script lang="ts">
    let {onclick, children} = $props();
</script>

<button onclick={onclick} class="bg-black px-4 py-2 cursor-pointer">
    {@render children()}
</button>