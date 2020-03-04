<script>
  import Tile from './Tile.svelte';
  import { onMount } from 'svelte';

  export let size;
  export let image;

  let ulElement;
  let tiles = [];
  let tilesSetup = [];
  let blankTile;

  onMount(() => {
    ulElement.style.setProperty('--image', image);
    ulElement.style.setProperty('--grid-size', size);
    createTiles();
  });

  function createTiles() {
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

<style>
  ul{
    padding: 0;
    margin: 0;
    width: var(--content-size);
    height: var(--content-size);
    display: grid;
    grid-gap: .5em;
    margin-bottom: 4em;
  }

</style>