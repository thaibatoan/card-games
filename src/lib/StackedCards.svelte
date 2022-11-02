<script>
export let number = 0;
</script>
  
<div class="wrapper">
  <div class="image">
    <img style="left: 0; top: 0; transform: rotate({number/2}deg);transform-origin: 50% 100%;" src="src/assets/back.svg" class="card" />
    <div class="indicator" style="translate: -{number/2 + 1}px -5px">{number}</div>
    
  </div>
  {#each new Array(number) as card, i (i)}
    <div class="image">
      <img style="left: -{i}px; top: {i}px; transform: rotate({-i + number/2}deg);transform-origin: 50% 100%;" src="src/assets/back.svg" class="card" />
    </div>
  {/each}
</div>

<style>
  .wrapper {
    position: relative;
    width: 8em;
    height: 8em;
  }
  img {
    height: 6em;
  }
  .image {
    position: absolute;
  }
  .image .indicator {
    position: absolute;
    font-size: 2em;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    z-index: 10;
    font-family: 'Source Code Pro';
  }
</style>