<script lang="ts">
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

    let player: string = 'X';
    let winner: string;
    let headerText: string;
    $: headerText = `Current player: ${player}`
    let canvas: HTMLCanvasElement;

    function updateBoard(index: number) {
        if (winner === undefined && fields[index] <= '9')  { //'O' and 'X' have a higher ASCII value than '0' to '9'
            fields[index] = player;

            calculateWinner(index);
            if (correctPatterns.length > 0) {
                winner = player;
                headerText = `The winner is ${winner}!`

                for (const pattern of correctPatterns) {
                    drawLine(pattern);
                }
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

    function calculateWinner(index: number) {
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
                correctPatterns.push(pattern);
                correctPatterns = correctPatterns;
            }
        }
    }

    function drawLine(nums: number[]) {
        const size = canvas.clientHeight;
        canvas.setAttribute("width", size.toString());
        canvas.setAttribute("height", size.toString());
        const buttonSize = size*0.27;   //buttons are 27% height/width
        const ctx = canvas.getContext("2d");
        if (ctx !== null) {
            ctx.beginPath();
            ctx.strokeStyle = "red";
            /*
            * size/2 = middle
            * size/2 - size/4 = left or top
            * size/2 + size/4 = right or bottom
            */
            const firstPoint = [size/2 + size/4 * (nums[0]%3 - 1), size/2 + size/4 * (Math.floor(nums[0]/3) - 1)];
            const secondPoint = [size/2 + size/4 * (nums[2]%3 - 1), size/2 + size/4 * (Math.floor(nums[2]/3) - 1)];
            ctx.moveTo(firstPoint[0], firstPoint[1]);
            ctx.lineTo(secondPoint[0], secondPoint[1]);
            console.log(`Size: ${size}\nPoint 1: (${firstPoint[0]}, ${firstPoint[1]})\nPoint 2: (${secondPoint[0]}, ${secondPoint[1]})`);
            ctx.stroke(); //draw the line
        }
    }
</script>

<body class="main">
    <h1>{headerText}</h1>
    <div class="board">
        <canvas class="lines" bind:this={canvas}></canvas>
        {#each fields as num}
            <button on:click={ () => updateBoard(parseInt(num)-1) }>{num}</button>
        {/each}
    </div>
</body>

<style>
    .main {
        background-color: rgb(47, 44, 48);
    }

    .main h1 {
        font-family: Arial, Helvetica, sans-serif;
        color: aliceblue;
        text-align: center;
    }

    .board {
        width: 50vh;
        height: 50vh;
        background-color: rgb(75, 72, 72);
        margin: auto;
        display: flex;
        flex-direction: row;
        flex-wrap: wrap;
        justify-content: space-evenly;
        position: relative;
    }

    .lines {
        height: 100%;
        width: 100%;
        position: absolute;
        pointer-events: none;
    }

    button {
        background-color: rgb(110, 106, 106);
        color: aliceblue;
        height: 27%;
        width: 27%;
        margin: 1%;
        border: none;
        font-size: xx-large;
    }

    button:hover {
        background-color: rgb(134, 129, 129);
    }

    button:active {
        background-color: rgb(158, 152, 152);
    }
</style>