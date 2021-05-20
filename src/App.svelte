<script>
	import GamePad from "../components/GamePad.svelte";
	let result = "";
</script>
<style>
	.title {
		color: rgb(238, 228, 228);
		text-align: center;
	}
	.right {
		color: rgb(238, 228, 228);
		margin-left: 80%;
	}
	.gamepad {
		width: 500px;
		height: 500px;
		margin: auto;
	}
</style>

<h1 class="title">Tic Tac Toe!</h1>
<h3 class="right">Result: {result} </h3>
<div class="gamepad">
	<GamePad bind:result={result}/>
</div>