<script lang="ts">
    let hints_x = [[1],[1,1],[1],[4]];
    let hints_y = [[1,1],[1,1],[1],[3]]; 
    let row = {
        horizontal: hints_x.map((_, x) => x),
        vertical: hints_y.map((_, y) => y)
    }
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

        if (checkedX.toString() === hints_x[x].toString()) {
            row.horizontal = row.horizontal.filter(number => number !== x);
        } else if (!row.horizontal.includes(x)) {
            row.horizontal = [x, ...row.horizontal]
        }

        if (checkedY.toString() === hints_y[y].toString()) {
            row.vertical = row.vertical.filter(number => number !== y);
        } else if (!row.vertical.includes(y)) {
            row.vertical = [y, ...row.vertical]
        }
    }
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<!-- svelte-ignore a11y_mouse_events_have_key_events -->
<div 
    class="w-fit grid grid-rows-[auto_1fr] grid-cols-[auto_1fr]" 
    on:mouseup={() => isDragged = false}
>
    <div></div>
    <div class="flex items-end">
        {#each hints_y as hint_y, index}
            <div class="flex flex-col">
                {#each hint_y as hint}
                    <div class="w-10 h-8 bg-black flex justify-center items-center">
                        <span class="{row.vertical.includes(index) ? '' : 'opacity-20'}">
                            {hint}
                        </span>
                    </div>
                {/each}
            </div>
        {/each}
    </div>
    <div class="flex flex-col">
        {#each hints_x as hint_x, index}
            <div class="flex justify-end">
                {#each hint_x as hint}
                    <div class="w-8 h-10 bg-black flex justify-center items-center">
                        <span class="{row.horizontal.includes(index) ? '' : 'opacity-20'}">
                            {hint}
                        </span>
                    </div>
                {/each}
            </div>
        {/each}
    </div>
    <div class="divide-y-2 divide-black grid border-2 border-black bg-white">
        {#each {length: hints_x.length}, x}
            <div class="flex divide-x-2 divide-black">
                {#each {length: hints_y.length}, y}
                    <button
                        on:mousedown={() => drag(x, y)}
                        on:mouseover={() => {if (isDragged) fill(x, y)}}
                        aria-label="grid button"
                        class="{grid[x][y] === 1 ? 'x' : ''} bg-white relative w-full h-full overflow-hidden"
                    >
                        <div class="{row.horizontal.includes(x) ? '' : 'line-x'} {row.vertical.includes(y) ? '' : 'line-y'} opacity-30"></div>
                    </button>
                {/each}
            </div>
        {/each}
    </div>
    <div class="fixed grid gap-4 left-4 bottom-4 bg-black p-2">
        <h1 class="text-center p-2 bg-white/5 -m-2">
            Incorrect rows and columns
        </h1>
        <div class="grid grid-cols-[auto_1fr] gap-x-4 p-2">
            <div class="text-right">
                Rows
            </div> 
            <div class="divide-x-2 divide-white/10 flex bg-white/5 p-1">
                {#if row.horizontal.length > 0}
                    {#each row.horizontal.sort() as el}
                        <span class="px-2">
                            {el + 1}
                        </span>
                    {/each}
                {:else}
                    <span class="opacity-20 italic">
                        all good
                    </span>
                {/if}
            </div>
            <div class="text-right">
                Columns
            </div>
            <div class="divide-x-2 divide-white/10 flex bg-white/5 p-1">
                {#if row.vertical.length > 0}
                    {#each row.vertical.sort() as el}
                        <span class="px-2">
                            {el + 1}
                        </span>
                    {/each}
                {:else}
                    <span class="opacity-20 italic">
                        all good
                    </span>
                {/if}
            </div>
        </div>
    </div>
</div>

<style scoped>
.x::before {
  background-color: black;
  content: "";
  width: 2px;
  height: 150%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
  position: absolute;
}

.x::after {
  background-color: black;
  content: "";
  width: 2px;
  height: 150%;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%) rotate(-45deg);
  position: absolute;
}

.line-x::before {
  background-color: red;
  content: "";
  width: 100%;
  height: 2px;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
}

.line-y::after {
  background-color: red;
  content: "";
  width: 2px;
  height: 100%; 
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
}
</style>