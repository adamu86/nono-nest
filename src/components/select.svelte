<script lang="ts">
    let clicked = $state(false);
    let value = $state();
    let {
        options = [], 
        select = $bindable(), 
        placeholder = 'Select'
    } = $props();

    const onClick = (el: {name: string, value: string | number}) => {
        select = el.value;
        value = el.name;
        clicked = false;
    }
</script>

<!-- svelte-ignore a11y_role_has_required_aria_props -->
<!-- svelte-ignore a11y_click_events_have_key_events -->
<!-- svelte-ignore a11y_no_noninteractive_element_interactions -->
<div class="grid grid-rows-[auto_1fr] bg-black text-white">
    <ol class="cursor-pointer order-1">
        {#if clicked}
            {#each options as el}
                <li class="bg-black p-2 hover:inset-ring-1" onclick={() => onClick(el)}>
                    {el.name}
                </li>
            {/each}
        {/if}
    </ol>
    <input 
        onclick={() => clicked = !clicked} 
        class="cursor-pointer outline-none m-2 min-w-0 order-2"
        role="combobox"
        value={value} 
        placeholder={placeholder}
        readonly
    > 
</div>