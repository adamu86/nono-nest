<script lang="ts">
    let hints_x = [[1],[1],[1],[1],[1],[1]];
    let hints_y = [[1],[1],[1],[1],[1],[1]]; 
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

        fill(x, y);
    }

    const fill = (x: number, y: number) => {
        if (isDragged) {
            grid[x][y] = initialHit;
        }

        const checkedX: number[] = [];
        let sumX = 0;

        for (let col = 0; col < grid[x].length; col += 1) {
            if (grid[x][col] === 1) {
                sumX += 1;
            } else if (grid[x][col] === 0 && sumX > 0) {
                checkedX.push(sumX);
                sumX = 0;
            }
        }
        if (sumX > 0) {
            checkedX.push(sumX);
        }

        const checkedY: number[] = [];
        let sumY = 0;

        for (let row = 0; row < grid.length; row += 1) {
            if (grid[row][y] === 1) {
                sumY += 1;
            } else if (grid[row][y] === 0 && sumY > 0) {
                checkedY.push(sumY);
                sumY = 0;
            }
        }
        if (sumY > 0) {
            checkedY.push(sumY);
        }
    }

    // na początku wszystkie kombinacje z grida
    // usuwamy jeśli się zgadza, jeśli się nie zgadza dodajemy z powrotem
    // jeśli wszystkie z kolejki usunięte = koniec gry
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<div 
    class="w-fit grid grid-rows-[auto_1fr] grid-cols-[auto_1fr] p-1" 
    on:mouseup={() => isDragged = false}
>
    <div></div>
    <div class="flex items-end divide-x-1 divide-white/40">
        {#each hints_y as hint_y}
            <div class="flex flex-col divide-y-1 divide-white/20">
                {#each hint_y as hint}
                    <div class="w-10 h-8 bg-black flex justify-center items-center">
                        {hint}
                    </div>
                {/each}
            </div>
        {/each}
    </div>
    <div class="flex flex-col items-end divide-y-1 divide-white/40">
        {#each hints_x as hint_x}
            <div class="flex justify-end divide-x-1 divide-white/20">
                {#each hint_x as hint}
                    <div class="w-8 h-10 bg-black flex justify-center items-center">
                        {hint}
                    </div>
                {/each}
            </div>
        {/each}
    </div>
    <div class="divide-y-1 divide-black grid">
        {#each {length: hints_x.length}, x}
            <div class="flex divide-x-1 divide-black">
                {#each {length: hints_y.length}, y}
                    <!-- svelte-ignore a11y_mouse_events_have_key_events -->
                    <button
                        on:mousedown={() => drag(x, y)}
                        on:mouseover={() => fill(x, y)}
                        aria-label="grid button"
                        class="{grid[x][y] === 1 ? 'x' : ''} bg-white relative w-full h-full"
                    >
                    </button>
                {/each}
            </div>
        {/each}
    </div>
    <!-- <div class="fixed bottom-4 left-4 bg-black/10 p-4">
        {#each grid as rows}
            <div class="flex gap-3">
                {#each rows as tile}
                    <div>
                        {tile}
                    </div>
                {/each}
            </div>
        {/each}
    </div> -->
</div>

<style scoped>
.x::before {
  background-color: black;
  content: "";
  width: 0.1rem;
  height: 100%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
  position: absolute;
}

.x::after {
  background-color: black;
  content: "";
  width: 0.1rem;
  height: 100%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%) rotate(-45deg);
  position: absolute;
}
</style>