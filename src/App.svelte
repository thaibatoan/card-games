<script lang="ts">
  import svelteLogo from './assets/svelte.svg'
  import Counter from './lib/Counter.svelte'
  import StackedCards from './lib/StackedCards.svelte'
  import { fly } from "svelte/transition";
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';



	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),
  });
  const cards = ["3", "4", "5", "6", "7", "8", "9", "10", "J", "Q", "K", "A", "2"]
  const cardsMap = Object.fromEntries(Object.entries(cards).map(([order, value])=> ([value, order])))

  const cardTypes = ['♠️','♣️','♦️','♥️']
  const cardTypesMap = {
    '♠️': 0,'♣️': 1,'♦️': 2,'♥️': 3
  }

  let currentCards = randomHand();

  let playedCards = [
    {cards: ['3♠️', '4♠️', '5♠️'], rotation: randomInt(-30,30), x: randomInt(-50,50), y: randomInt(-50,50)},
    {cards: ['6♠️', '7♠️', '8♠️'], rotation: randomInt(-30, 30), x: randomInt(-50,50), y: randomInt(-50,50)},
    {cards: ['9♠️', '10♠️', 'J♠️'], rotation: randomInt(-30, 30), x: randomInt(-50,50), y: randomInt(-50,50)},
  ]

  function selectCard(card) {
    const selectedCard = currentCards.find(c => c.card === card);
    selectedCard.selected = !selectedCard.selected;
    currentCards = [...currentCards];
  }

  function randomInt(from, to) {
    return Math.floor(Math.random() * (to - from + 1) + from)
  }

  let number = 0

  let showHand = false;

  function sendCards() {
    const selectedCards = currentCards.filter(({selected}) => selected).map(({card}) => card);
    playedCards = [...playedCards, {cards: selectedCards, rotation: 0, x: 0, y: randomInt(-50,50)}]
    currentCards = currentCards.filter(({selected}) => !selected);
  }

  function dealCards() {
    if (showHand) {
      showHand = false
      currentCards = randomHand()
      number = 0;
    } else {
      showHand = true;
      setTimeout(() => {
        currentCards = currentCards.sort(({card: card1}, {card: card2}) => {
          const number1 = card1.slice(0, -2);
          const type1 = card1.at(-2);
          const number2 = card2.slice(0, -2);
          const type2 = card2.at(-2);
          return (cardsMap[number1] - cardsMap[number2]) || (cardTypesMap[type1] - cardTypesMap[type2])
        })
      }, 1800);
      const timer = setInterval(myFunction, 100);

      function myFunction() {
        number++;
        if(number >= 13) {
          clearInterval(timer);
          return;
        }
      }}
  }

  function randomHand() {
    const result = new Set()
    while (result.size < 13) {
      const cardNumber = cards[randomInt(0, 12)];
      const cardType = cardTypes[randomInt(0, 3)];
      result.add(`${cardNumber}${cardType}`)
    }
    return [...result].map(card => ({card, selected: false}))
  }

  function isValid() {
    const selectedCards = currentCards.filter(({selected}) => selected).map(({card}) => card);
    if (playedCards.length > 0) {
      const lastCards = playedCards.at(-1).cards;
    }
  }

</script>

<main>

  <div style="display: flex; justify-content: space-between">
    <StackedCards number={number}/>
    <StackedCards number={number}/>
    <StackedCards number={number}/>

  </div>

  <div class="board" style="position: relative; height: 300px">
    {#each playedCards as {cards, rotation,x ,y}}
    <div class="board-item" style="rotate: {rotation}deg; translate: {x}px {y}px">
      {#each cards as card}
          <div class="image">
            <img in:receive="{{key: card}}" src="src/assets/{card}.svg" class="logo" />
          </div>
      {/each}
    </div>
    {/each}
  </div>
  
  {#if showHand}
    <div class="hand">
        {#each currentCards as {card,selected}, i (card)}
          <img in:fly="{{ y: -50, duration: 500, delay: i*100 }}" 
				    out:send="{{key: card}}"
            animate:flip={{duration:400}}
          style="left: {i *20}px" class:selected="{selected}" src="src/assets/{card}.svg" class="logo" alt="Svelte Logo" on:click={() => selectCard(card)} />
        {/each}

      </div>
  {/if}

<div style="padding: 2em">
    <button on:click={sendCards}>Send</button>
    <button on:click={dealCards}>Deal</button>
</div>

    {#each cardTypes as cardType}
      <div>
        {#each cards as card}
            <img src="src/assets/{card}{cardType}.svg" class="logo" alt="Svelte Logo" />
        {/each}
      </div>
    {/each}
</main>

<style>
  .logo {
    height: 6em;
    will-change: filter;
  }
  .read-the-docs {
    color: #888;
  }

  .board-item {
    position: absolute; 
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    pointer-events: none;
  }
  .board-item .image {
    position: relative;
  }

  .board-item:not(:last-child) .image::after{
    position: absolute;
    content: "";
    background-color: black;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    border-radius: 10px;
    opacity: 50%;
  }

  .hand {
    position: relative;
    height: 6em;
  }
  .hand img {
    position: absolute;
    top: 0;
    left: 0;
  }
  .hand img.selected {
    top: -15px;
  }
  .hand img:hover {
    z-index: 99;
    filter: drop-shadow(0 0 2em #646cffaa);
  }
</style>
