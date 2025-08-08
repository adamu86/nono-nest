<script lang="ts">
    let hints_x: number[][] = [];
    let hints_y: number[][] = [];
    let grid: number[][] = [];
    let rows: {
        horizontal: number[], 
        vertical: number[]
    } = {
        horizontal: [], 
        vertical: []
    };
    
    let initialHit: number;
    let isDragged = false;
    
    const drag = (event: MouseEvent, x: number, y: number) => {
        isDragged = true;

        switch(grid[x][y]) {
            case 0:
                if (event.button === 0) {
                    initialHit = 1;
                } else if (event.button === 2) {
                    initialHit = -1;
                }
                break;
            case 1:
                if (event.button === 0) {
                    initialHit = 0;
                } else if (event.button === 2) {
                    initialHit = -1;
                }
                break;
            case -1:
                if (event.button === 0) {
                    initialHit = 1;
                } else if (event.button === 2) {
                    initialHit = 0;
                }
                break;
        }

        fill(x, y);
    }

    const fill = (x: number, y: number) => {
        if (isDragged) {
            grid[x][y] = initialHit;
        }

        const tempX: number[] = [];
        let sumX = 0;

        for (let col = 0; col < grid[x].length; col += 1) {
            if (grid[x][col] === 1) {
                sumX += 1;
            } else if (grid[x][col] <= 0 && sumX > 0) {
                tempX.push(sumX);
                sumX = 0;
            }
        }
        if (sumX > 0) {
            tempX.push(sumX);
        }

        const tempY: number[] = [];
        let sumY = 0;

        for (let row = 0; row < grid.length; row += 1) {
            if (grid[row][y] === 1) {
                sumY += 1;
            } else if (grid[row][y] <= 0 && sumY > 0) {
                tempY.push(sumY);
                sumY = 0;
            }
        }
        if (sumY > 0) {
            tempY.push(sumY);
        }

        if (tempX.toString() === hints_x[x].toString()) {
            rows.horizontal = rows.horizontal.filter(number => number !== x);
        } else if (!rows.horizontal.includes(x)) {
            rows.horizontal = [x, ...rows.horizontal]
        }

        if (tempY.toString() === hints_y[y].toString()) {
            rows.vertical = rows.vertical.filter(number => number !== y);
        } else if (!rows.vertical.includes(y)) {
            rows.vertical = [y, ...rows.vertical]
        }
    }

    export const random = (size: number) => {
        hints_x = [];
        hints_y = [];
        grid = [];

        const probability = 0.5;
        const tempGrid = Array.from({length: size}).map(_ => Array.from({length: size}).map(_ => 0));

        for (let row = 0; row < size; row++) {
            for (let col = 0; col < size; col++) {
               tempGrid[row][col] = Math.random() < probability ? 1 : 0;
            }
        }

        let tempX: number[] = [];
        for (let row = 0; row < tempGrid.length; row += 1) {
            let sum = 0;

            for (let col = 0; col < tempGrid[row].length; col += 1) {
                if (tempGrid[row][col] === 1) {
                    sum += 1;
                } else if (tempGrid[row][col] <= 0 && sum > 0) {
                    tempX.push(sum);
                    sum = 0;
                }
            }
            if (sum > 0) tempX.push(sum);

            hints_x.push(tempX);
            tempX = []; 
        }

        let tempY: number[] = [];
        for (let col = 0; col < tempGrid.length; col += 1) {
            let sum = 0;

            for (let row = 0; row < tempGrid[col].length; row += 1) {
                if (tempGrid[row][col] === 1) {
                    sum += 1;
                } else if (tempGrid[row][col] <= 0 && sum > 0) {
                    tempY.push(sum);
                    sum = 0;
                }
            }
            if (sum > 0) tempY.push(sum);

            hints_y.push(tempY);
            tempY = []; 
        }   
        
        if (hints_x.some(arr => arr.length === 0) || hints_y.some(arr => arr.length === 0)) {
            random(size);
        }

        rows = {
            horizontal: hints_x.map((_, x) => x),
            vertical: hints_y.map((_, y) => y)
        }

        grid = Array.from({length: size}).map(_ => Array.from({length: size}).map(_ => 0));
    }

    $: if (rows.horizontal.length === 0 && rows.vertical.length === 0) {
        
    }
</script>

<!-- svelte-ignore a11y_no_static_element_interactions -->
<!-- svelte-ignore a11y_mouse_events_have_key_events -->
{#if grid.length > 0}
    <div class="grid gap-4">
        <div 
            class="w-fit grid grid-rows-[auto_1fr] grid-cols-[auto_1fr]" 
            on:mouseup={() => isDragged = false}
            on:mouseleave={() => isDragged = false}
            on:keypress={() =>  null}
        >
            <div></div>
            <div class="flex items-end">
                {#each hints_y as hint_y, index}
                    <div class="flex flex-col">
                        {#each hint_y as hint}
                            <div class="w-10 h-8 bg-black flex justify-center items-center">
                                <span class="{rows.vertical.includes(index) ? '' : 'opacity-20'}">
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
                                <span class="{rows.horizontal.includes(index) ? '' : 'opacity-20'}">
                                    {hint}
                                </span>
                            </div>
                        {/each}
                    </div>
                {/each}
            </div>
            <div class="divide-y-2 divide-black grid border-2 border-black bg-white relative">
                {#each {length: hints_x.length}, x}
                    <div class="flex divide-x-2 divide-black">
                        {#each {length: hints_y.length}, y}
                            <button
                                on:contextmenu|preventDefault
                                on:mousedown|preventDefault={(event) => drag(event, x, y)}    
                                on:mouseover={() => {if (isDragged) fill(x, y)}}
                                aria-label="grid button"
                                class="{grid[x][y] === 1 ? '!bg-black/75' : (grid[x][y] === -1 ? 'x' : 'bg-white')} hover:bg-black/10 relative w-full h-full overflow-hidden"
                            >
                                <div class="{rows.horizontal.includes(x) ? '' : 'line-x'} {rows.vertical.includes(y) ? '' : 'line-y'} opacity-30"></div>
                            </button>
                        {/each}
                    </div>
                {/each}
            </div>
        </div>
    </div>
    <div class="fixed right-4 top-4 text-center gap-2 bg-black grid grid-cols-2 p-2">
        <div class="my-auto px-1">
            Rows
        </div> 
        <div class="my-auto px-1">
            Cols
        </div>
        <div class="divide-y-2 divide-white/10 w-full flex flex-col bg-white/10 p-1">
            {#each {length: grid.length}, el}
                <span class="px-2 py-1 {!rows.horizontal.includes(el) ? 'text-white/20' : ''}">
                    {el + 1}
                </span>
            {/each}
        </div>
        <div class="divide-y-2 divide-white/10 w-full flex flex-col bg-white/10 p-1">
            {#each {length: grid[0].length}, el}
                <span class="px-2 py-1 {!rows.vertical.includes(el) ? 'text-white/20' : ''}">
                    {el + 1}
                </span>
            {/each}
        </div>
    </div>
{/if}

<style scoped>
.x::before,
.x::after {
  background-color: black;
  content: "";
  width: 2px;
  height: 150%;
  left: 50%;
  top: 50%;
  position: absolute;
}
.x::before {
  transform: translate(-50%, -50%) rotate(45deg);
}
.x::after {
  transform: translate(-50%, -50%) rotate(-45deg);
}

.line-x::before,
.line-y::after {
  background-color: red;
  content: "";
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  position: absolute;
}
.line-x::before {
  width: 100%;
  height: 2px;
}
.line-y::after {
  width: 2px;
  height: 100%;
}
</style>