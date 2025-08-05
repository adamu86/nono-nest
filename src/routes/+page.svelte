<script lang="ts">
    let hints_x = [[1,2,3],[1,2],[1]];
    let hints_y = [[1],[1,2],[1,2,3]]; 
    let grid = hints_x.map(_ => hints_y.map(_ => 0));
    let initialHit: number;
    let isDragged = false;

    const drag = (x: number, y: number) => {
        isDragged = true;

        switch(grid[x][y]) {
            case 0:
                initialHit = 1;
                break;
            case 1:
                initialHit = 0;
                break;
        }

        color(x, y);
    }

    const color = (x: number, y: number) => {
        if (isDragged) {
            grid[x][y] = initialHit;
        }
    }
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<div 
    class="w-fit grid grid-rows-2 grid-cols-2" 
    on:mouseup={() => isDragged = false}
>
    <div></div>
    <div class="flex items-end">
        {#each hints_y as hint_y}
            <div class="flex flex-col">
                {#each hint_y as hint}
                    <div class="w-10 h-10 bg-white/5 flex justify-center items-center">
                        {hint}
                    </div>
                {/each}
            </div>
        {/each}
    </div>
    <div class="flex flex-col">
        {#each hints_x as hint_x}
            <div class="flex justify-end">
                {#each hint_x as hint}
                    <div class="w-10 h-10 bg-white/5 flex justify-center items-center">
                        {hint}
                    </div>
                {/each}
            </div>
        {/each}
    </div>
    <div>
        {#each {length: hints_x.length}, x}
            <div class="flex">
                {#each {length: hints_y.length}, y}
                    <!-- svelte-ignore a11y_mouse_events_have_key_events -->
                    <button
                        on:mousedown={() => drag(x, y)}
                        on:mouseover={() => color(x, y)}
                        aria-label="grid button"
                        class="{grid[x][y] === 1 ? 'x' : ''} bg-slate-100 relative border-b-1 border-r-1 border-black w-10 h-10"
                    >
                    </button>
                {/each}
            </div>
        {/each}
    </div>
    <div class="fixed bottom-4 left-4 bg-black/10 p-4">
        {#each grid as rows}
            <div class="flex gap-3">
                {#each rows as tile}
                    <div>
                        {tile}
                    </div>
                {/each}
            </div>
        {/each}
    </div>
</div>

<style scoped>
.x::before {
  background-color: #374151;
  content: "";
  border-radius: 0.75rem;
  width: 0.25rem;
  height: 100%;
  left: 45%;
  top: 0%;
  transform: rotate(-45deg);
  position: absolute;
}

.x::after {
  background-color: #374151;
  content: "";
  border-radius: 0.75rem;
  width: 0.25rem;
  height: 100%;
  left: 45%;
  top: 0%;
  transform: rotate(45deg);
  position: absolute;
}
</style>