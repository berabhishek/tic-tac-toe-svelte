<script>
    import GameUnit from "./GameUnit.svelte";
    export let result = "Draw";
    let current_symbol = "X";
    let filled_count = 0;
    let board = [];
    let player = "O";
    let opponent = "X";
    let game_over = false;
    let initBoard = function() {
        board = [];
        for(let i = 0; i< 3; i++) {
            board.push(new Array(3).fill(null));
        }
    }

    initBoard();
    let resetBoard = function() {
        let elements = document.getElementsByClassName("unit");
        for(let item of elements) {
            item.innerHTML = "";
        }
        current_symbol = "X";
        initBoard();
        filled_count = 0;
        game_over = false;
        result = "";
    }

    let toggleMarker = function () {
        if (current_symbol === "X") {
            current_symbol = "O";
        } else {
            current_symbol = "X";
        }
    }

    let drawMarker = function(element) {
        element.innerHTML = `<span class="singleunit">${current_symbol}</span>`;
        let i = parseInt(element.getAttribute("i"));
        let j = parseInt(element.getAttribute("j"));
        board[i][j] = current_symbol;
        filled_count += 1;
        toggleMarker(); 
    }

    let onCellClick = function(event) {
        if(event.target.innerHTML !== "" || game_over) {
            return;
        }
        drawMarker(event.target);
        postClickProcess();
    }

    let evaluate = function() {
        for (let i = 0; i <= 2; i++) {
            if (checkPostions(board[i][0], board[i][1], board[i][2])) {
                if (board[i][0] === player) {
                    return 10;
                } else if (board[i][0] === opponent) {
                    return -10;
                }
            }
        }
        for (let j = 0; j <= 2; j++) {
           if (checkPostions(board[0][j], board[1][j], board[2][j])) {
                if (board[0][j] === player) {
                    return 10;
                } else if (board[0][j] === opponent) {
                    return -10;
                }
           }
        }
        let diagonal_a = [];
        let diagonal_b = [];
        for (let i=0; i <= 2; i++) {
            for (let j=0; j<=2; j++) {
                if (i+j === 2) {
                    diagonal_b.push(board[i][j]);
                }
                if (i === j) {
                    diagonal_a.push(board[i][j]);
                }
            }
        }
        if (checkPostions(...diagonal_a)) {
            if (diagonal_a[0] === player) {
                return 10;
            } else if (diagonal_a[0] === opponent) {
                return -10;
            }
        }
        if (checkPostions(...diagonal_b)) {
            if (diagonal_b[0] === player) {
                return 10;
            } else if (diagonal_b[0] === opponent) {
                return -10;
            }
        }
        return 0;
    }

    let checkPostions = function(a, b , c) {
        let res = (
            a === b && b === c && a !== null && b !== null && c !== null
        );
        return res;
    }

    let checkGameFinished = function() {
        let evaluation = evaluate();
        if (evaluation!== 0 || filled_count === 9) {
            game_over = true;
            if (evaluation === 10) {
                result = player + " Wins!";
            } else if (evaluation === -10) {
                result = opponent + " Wins!";
            } else {
                result = "Draw";
            }
        }
        return evaluation !== 0 || filled_count === 9;    
    
    }

    let postClickProcess = function() {
        let finished = checkGameFinished();
        if(!finished && current_symbol === player) {
           playComputer();
        }
    }


    // Game logic - CPU
    let isMovesLeft = function (board) {
        for (let i=0; i < 3; i++) {
            for (let j=0; j < 3; j++) {
                if (board[i][j] === null) {
                    return true;
                }
            }
        }
        return false;
    }

    let playComputer = function() {
        let elements = document.getElementsByClassName("unit");
        let best_move = findBestMove(board);
        let pointer = best_move[0] * 3 + best_move[1];
        for(let i=0; i<9; i++) {
            if (i === pointer) {
                drawMarker(elements[i]);
                checkGameFinished();
                break;
            }
        }
    }

    let minimax = function(board, depth, isMax) {
        let score = evaluate(board);

        if (score === 10 || score === -10) {
            return score;
        }
        if (isMovesLeft(board) === false) {
            return 0;
        }

        if (isMax) {
            let best = -1000;
            for (let i=0; i<3; i++) {
                for (let j=0; j<3; j++) {
                    if (board[i][j] === null) {
                        board[i][j] = player;
                        best = Math.max(best, minimax(board, depth+1, !isMax));
                        board[i][j] = null;
                    }
                }
            }
            return best;
        } else {
            let best = 1000;
            for (let i=0; i<3; i++) {
                for (let j=0; j<3; j++) {
                    if (board[i][j] === null) {
                        board[i][j] = opponent;
                        best = Math.min(best, minimax(board, depth+1, !isMax));
                        board[i][j] = null;
                    }
                }
            }
            return best;
        }



    }

    let findBestMove = function(board) {
        let bestValue = -1000;
        let bestMove = [-1, -1];
        for (let i=0; i<=2; i++) {
            for (let j=0; j <=2 ; j++) {
                if (board[i][j] === null) {
                    board[i][j] = player;
                    let moveVal = minimax(board, 0, false);
                    board[i][j] = null;

                    if (moveVal > bestValue) {
                        bestMove = [i, j];
                        bestValue = moveVal;
                    } 
                }
            }
        }
        return bestMove;
    }


</script>
<style>
    .game {
        display: flex;
        flex-wrap: wrap;
        flex-direction: row;
        align-content: flex-start;
        width: 100%;
        height: 100%;
    }
    .play-again {
        position: absolute;
        margin-right: 10px;
    }
</style>
<div class="game" on:click={onCellClick}>
    {#each [0,1,2] as i}
        {#each [0,1,2] as j}
            <GameUnit index={[i,j]} />
        {/each}
    {/each}
</div>
<button class="play-again" on:click={resetBoard}>Play Again!</button>