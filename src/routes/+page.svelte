<script lang="ts">
    import { to_number } from "svelte/internal";

    class Pattern {
        fieldNums: number[];
        rotate: number;

        constructor(numbers: number[], rotationDegrees: number) {
            this.fieldNums = numbers;
            this.rotate = rotationDegrees;
        }
    }

    const patterns: Pattern[] = [
    new Pattern([0, 1, 2], 0), new Pattern([3, 4, 5], 0), new Pattern([6, 7, 8], 0),       //horizontal
    new Pattern([0, 3, 6], 90), new Pattern([1, 4, 7], 90), new Pattern([2, 5, 8], 90),    //vertical
    new Pattern([0, 4, 8], 45), new Pattern([2, 4, 6], 135)                                //diagonal
    ];

    const fields: string[] = [];
    for (let i = 1; i <= 9; i++) {
        fields.push(i.toString());
    }
    let correctPatterns: Pattern[] = [];

    let player: string = 'X';
    let winner: string;
    let headerText: string;
    $: headerText = `Current player: ${player}`

    function updateBoard(index: number) {
        if (winner === undefined && fields[index] <= '9')  { //'O' and 'X' have a higher ASCII value than '0' to '9'
            fields[index] = player;

            calculateWinner(index);
            if (correctPatterns.length > 0) {
                winner = player;
                headerText = `The winner is ${winner}!`
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

        patterns.forEach(pattern => {
            goodPattern = true;
            pattern.fieldNums.forEach(num => {
                if (fields[num] !== player) {
                    goodPattern = false;
                    return; //breaks out of the forEach()
                }
            });

            if (goodPattern) {
                correctPatterns.push(pattern);
                correctPatterns = correctPatterns;
            }
        });
    }
</script>

<body id="main">
    <h1>{headerText}</h1>
    <div id="board">
        {#each correctPatterns as pattern}
            <div class="correctLine" style="top: {50+38.5*((Math.round(pattern.fieldNums[1]/3)-1))}%; transform: rotate({pattern.rotate}deg);"></div>
        {/each}
        {#each fields as num}
            <button on:click={ () => updateBoard(to_number(num)-1) }>{num}</button>
        {/each}
    </div>
</body>

<style>
    #main {
        background-color: rgb(47, 44, 48);
    }

    #main h1 {
        font-family: Arial, Helvetica, sans-serif;
        color: aliceblue;
        text-align: center;
    }

    #board {
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

    button {
        background-color: rgb(110, 106, 106);
        color: aliceblue;
        height: 27%;
        width: 27%;
        margin-top: auto;
        margin-bottom: auto;
        border: none;
        font-size: xx-large;
    }

    button:hover {
        background-color: rgb(134, 129, 129);
    }

    button:active {
        background-color: rgb(158, 152, 152);
    }

    .correctLine {
        width: 80%;
        height: 10%;
        margin: auto;
        background-color: rgb(221, 6, 6);
        position: absolute;
    }
</style>