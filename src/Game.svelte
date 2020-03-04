<script>
  import Tile from './Tile.svelte';
  import { onMount } from 'svelte';
  import animateCssGrid from 'animate-css-grid'
  import { tick } from 'svelte';

  export let size;
  export let image;

  let ulElement;
  let tiles = [];
  let tilesSetup = [];
  let blankTile;
  let animatedCssGrid;

  onMount(() => {
    ulElement.style.setProperty('--image', image);
    ulElement.style.setProperty('--grid-size', size);
    createTiles();
    animatedCssGrid = animateCssGrid.wrapGrid(ulElement,{
      duration: 500,
    });
  });

  let expanded = false;
  async function onExpandClick() {
    expanded = !expanded;
    document.documentElement.style.setProperty('--grid-gap', expanded ? '1em' : '0');
    await tick();
    animatedCssGrid.forceGridAnimation();
  }

  function createTiles() {
    tiles = [];
    tilesSetup = [];
    for (let row = 0; row < size; row++) {
      for (let col = 0; col < size; col++) {
        tilesSetup.push({row, col});
      }
    }
  }

  async function onTileClick(tile) {
    if(tile.canMoveTo(blankTile.currentRow, blankTile.currentCol)) {
      const {currentRow: prevRow, currentCol: prevCol} = tile;
      tile.moveTo(blankTile.currentRow, blankTile.currentCol);
      blankTile.moveTo(prevRow, prevCol);
      checkWinCondition();
      await tick();
      animatedCssGrid.forceGridAnimation();
    }
  }

  function checkWinCondition() {
    if(tiles.every(tile => tile.isInRightPlace())) {
      console.log('you win!');
    }
  }

  $: blankTile = tiles && tiles.length && tiles[tiles.length - 1];
</script>

<ul bind:this={ulElement}>
  {#each tilesSetup as tile, i}
    <Tile bind:this={tiles[i]}
      initialRow={tile.row}
      initialCol={tile.col}
      isBlank={blankTile && tiles[i] === blankTile}
      on:click={onTileClick(tiles[i])} />
  {/each}
</ul>

<button on:click={onExpandClick}>Expand</button>

<style>
  button {
      position: fixed;
      right: 0;
      bottom: 0;
  }

  ul{
    padding: 0;
    margin: 0;
    display: grid;
    grid-gap: var(--grid-gap, 0);
    margin-bottom: 4em;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    transition-property: all;
    transition-duration: 600ms;
    transition-timing-function: ease-in-out;
  }

</style>