<script>
	import { onMount } from "svelte";

	const name = "Jogo da Memória";

	let cards = [
		{id: 1, value: 'A', flipped: false, matched: false},
		{id: 2, value: 'A', flipped: false, matched: false},
		{id: 3, value: 'B', flipped: false, matched: false},
		{id: 4, value: 'B', flipped: false, matched: false},
		{id: 5, value: 'C', flipped: false, matched: false},
		{id: 6, value: 'C', flipped: false, matched: false},
		{id: 7, value: 'D', flipped: false, matched: false},
		{id: 8, value: 'D', flipped: false, matched: false},
	]

	let flippedCards = []
	let moves = 0
	let score = 0
	let isChecking = false;
	let gameOver = false;

	function shuffleCards() {
		cards = cards.sort(() => Math.random() - 0.5)
	}

	function flipCard(card) {
		if(flippedCards.length < 2 && !card.flipped && !card.matched && !isChecking && !gameOver){
			card.flipped = true
			cards = cards; 
			flippedCards = [...flippedCards, card]

			if(flippedCards.length === 2){
				moves++
				isChecking = true
				setTimeout(() => {
					checkMatch()
					isChecking = false
					checkGameOver()
				}, 1000)
			}
		}
	}

	function checkMatch(){
		const [card1, card2] = flippedCards;
		if(card1.value === card2.value){
			card1.matched = card2.matched = true
			score++
		} else {
			card1.flipped = card2.flipped = false
		}
		cards = cards;
		flippedCards = []
	}

	function checkGameOver() {
		if (cards.every(card => card.matched)) {
			gameOver = true;
		}
	}

	function resetGame() {
		cards = cards.map(card => ({...card, flipped: false, matched: false}));
		shuffleCards();
		moves = 0;
		score = 0;
		gameOver = false;
	}

	onMount(() => {
		shuffleCards()
	})
</script>

<main>
	<h1>{name}</h1>
	
	<p>Movimentos: {moves}</p>
	<p>Pontuação: {score}</p>

	<div class="grid">
		{#each cards as card (card.id)}
			<div
				class="card"
				class:flipped={card.flipped}
				class:matched={card.matched}
				on:click={() => flipCard(card)}
			>
				<div class="card-front">{card.value}</div>
				<div class="card-back"></div>
			</div>
		{/each}
	</div>

	{#if gameOver}
		<div class="game-over">
			<h2>Parabéns! Você completou o jogo!</h2>
			<p>Pontuação final: {score}</p>
			<p>Total de movimentos: {moves}</p>
			<button on:click={resetGame}>Jogar Novamente</button>
		</div>
	{/if}
</main>

<style>
	.grid {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  max-width: 400px;
  margin: 0 auto;
}

.card {
  aspect-ratio: 1;
  background-color: transparent;
  perspective: 1000px;
  width: 100%;
  height: 100px;
  cursor: pointer;
  border-radius: 10px;
}

.card-front, .card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2em;
  border-radius: 10px;
  transition: transform 0.6s, background-color 0.3s;
}

.card-back {
  background-color: #6c5ce7; 
  color: #fff; 
}

.card-front {
  background-color: #dfe6e9; 
  transform: rotateY(180deg);
  color: #2d3436; 
}

.card.flipped .card-front {
  transform: rotateY(0deg);
}

.card.flipped .card-back {
  transform: rotateY(180deg);
}

.card.matched .card-front {
  background-color: #55efc4;
}

.game-over {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  background-color: rgba(0, 0, 0, 0.8);
  color: white;
}

button {
  padding: 10px 20px;
  font-size: 1em;
  cursor: pointer;
  background-color: #74b9ff;
  color: #fff;
  border: none;
  border-radius: 5px;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #0984e3; 
}
</style>