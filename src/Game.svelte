<script>
  import Tile from './Tile.svelte';
  import { onMount } from 'svelte';

  export let size;
  export let image;

  let ulElement;
  let tiles = [];
  let tilesSetup = [];
  let blankTile;
  let expanded = false;;

  $: ulElement && ulElement.style.setProperty('--image', image);
  $: ulElement && ulElement.style.setProperty('--grid-size', size);
  $: createTiles(size);
  $: blankTile = tiles && tiles.length && tiles[tiles.length - 1];
  $: document.documentElement.style.setProperty('--grid-gap', expanded ? '1em' : '0');

  function createTiles(size) {
    tiles = [];
    tilesSetup = [];
    for (let row = 0; row < size; row++) {
      for (let col = 0; col < size; col++) {
        tilesSetup.push({row, col});
      }
    }
  }

  function onTileClick(tile) {
    if(tile.canMoveTo(blankTile.currentRow, blankTile.currentCol)) {
      const {currentRow: prevRow, currentCol: prevCol} = tile;
      tile.moveTo(blankTile.currentRow, blankTile.currentCol);
      blankTile.moveTo(prevRow, prevCol);
      checkWinCondition();
    }
  }

  function checkWinCondition() {
    if(tiles.every(tile => tile.isInRightPlace())) {
      console.log('you win!');
    }
  }
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

<button class="expand" on:click={() => expanded = !expanded }>Toggle Expand</button>

<style>
  button.expand {
      position: fixed;
      right: 0;
      bottom: 0;
  }

  ul {
    list-style-type: none;
    margin: 0;
    padding: 0;
    display: grid;
    grid-gap: var(--grid-gap, 0);
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    transition: all 600ms ease-in-out;
  }

</style>