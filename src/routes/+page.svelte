<script lang="ts">
    import { onMount } from "svelte";

    const patterns: number[][] = [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6]
    ];

    const fields: string[] = [];
    for (let i = 1; i <= 9; i++) {
        fields.push(i.toString());
    }
    let correctPatterns: number[][] = [];

    let player: 'X'|'O' = 'X';
    let winner: 'X'|'O'|undefined;
    let canvas: HTMLCanvasElement;
    let ctx: CanvasRenderingContext2D;
    let size: number;
    let populated = 0;

    onMount( () => {
        const c = canvas.getContext("2d");
        if (c) ctx = c;
    });

    function updateBoard(index: number) {
        if (winner === undefined && fields[index] <= '9')  { //'O' and 'X' have a higher ASCII value than '0' to '9'
            fields[index] = player;
            populated++;

            calculateWinner();
            if (correctPatterns.length > 0) {
                winner = player;
            }
            else {
                if (player === 'X') {
                    player = 'O';
                } else {
                    player = 'X';
                }
            }
        }
    }

    function calculateWinner() {
        let goodPattern: boolean;

        for (const pattern of patterns) {
            goodPattern = true;
            for (const num of pattern) {
                if (fields[num] !== player) {
                    goodPattern = false;
                    break;
                }
            }

            if (goodPattern) {
                if (!size)
                {
                    size = canvas.clientHeight;
                    canvas.setAttribute("width", size.toString() + 'px');
                    canvas.setAttribute("height", size.toString() + 'px');
                }
                drawLine(pattern);
                correctPatterns.push(pattern);
            }
        }
    }

    function drawLine(nums: number[]) {
        if (!ctx) return;
        const buttonSize = document.querySelector(".field")?.clientHeight;
        if (!buttonSize) return;
        const halfButtonSize = buttonSize / 2;
        //console.log(`Half button size is ${halfButtonSize}`);
        ctx.beginPath();
        ctx.strokeStyle = "red";
        ctx.lineWidth = 7;
        /*
        * size/2 = middle
        * offset goes to the edge of the button with "+ halfButtonSize"
        * then adds half of the distance from the edge of the button to the edge of the board
        * offset multiplied with either -1, 0 or 1
        */
        const firstPoint = [
            size/2 + (halfButtonSize + (size/2 - halfButtonSize)/2) * (nums[0]%3 - 1),
            size/2 + (halfButtonSize + (size/2 - halfButtonSize)/2) * (Math.floor(nums[0]/3) - 1)
        ];
        const secondPoint = [
            size/2 + (halfButtonSize + (size/2 - halfButtonSize)/2) * (nums[2]%3 - 1),
            size/2 + (halfButtonSize + (size/2 - halfButtonSize)/2) * (Math.floor(nums[2]/3) - 1)
        ];
        ctx.moveTo(firstPoint[0], firstPoint[1]);
        ctx.lineTo(secondPoint[0], secondPoint[1]);
        //console.log(`Size: ${size}\nPoint 1: (${firstPoint[0]}, ${firstPoint[1]})\nPoint 2: (${secondPoint[0]}, ${secondPoint[1]})`);
        ctx.stroke(); //draw the line
    }

    function reset() {
        populated = 0;
        correctPatterns = [];
        player = 'X';
        winner = undefined;
        for (let i = 0; i < 9; i++) {
            fields[i] = (i+1).toString();
        }

        //clear lines
        const ctx = canvas.getContext("2d");
        ctx?.clearRect(0, 0, canvas.width, canvas.height);
    }
</script>

<body class="main">
    <div class="column">
        <h1>
            {#if winner}
                The winner is {winner}!
            {:else}
                {#if populated === 9}
                    Draw!
                {:else}
                    Current player: {player}
                {/if}
            {/if}
        </h1>
        <div class="board">
            <canvas class="lines" bind:this={canvas}></canvas>
            {#each fields as num}
                <button class="field" on:click={ () => updateBoard(parseInt(num)-1) }>{num}</button>
            {/each}
        </div>
        <button class="reset" on:click={ () => reset() }>Reset</button>
    </div>
</body>

<style>
    .main {
        background-color: rgb(47, 44, 48);
    }

    h1 {
        font-family: Arial, Helvetica, sans-serif;
        color: aliceblue;
        text-align: center;
    }

    .column {
        display: flex;
        flex-direction: column;
    }

    .board {
        width: 50vh;
        height: 50vh;
        background-color: rgb(75, 72, 72);
        margin: auto;
        display: grid;
        grid-template-columns: 1fr 1fr 1fr;
        grid-template-rows: 1fr 1fr 1fr;
        gap: 2rem;
        padding: 2rem;
        position: relative;
    }

    .lines {
        height: 100%;
        width: 100%;
        position: absolute;
        pointer-events: none;
    }

    .field {
        background-color: rgb(110, 106, 106);
        color: aliceblue;
        border: none;
        font-size: xx-large;
    }

    .field:hover {
        background-color: rgb(134, 129, 129);
    }

    .field:active {
        background-color: rgb(158, 152, 152);
    }

    .reset {
        width: 10vh;
        height: 3vh;
        margin-left: auto;
        margin-right: auto;
        margin-top: 20px;
    }
</style>