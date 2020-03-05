<script>
    import { onMount } from 'svelte';

    export let isBlank;
    export let initialRow;
    export let initialCol;
    export let currentRow = 0;
    export let currentCol = 0;

    let liElement;

    $: currentRow = initialRow;
    $: currentCol = initialCol;
    $: liElement && liElement.style.setProperty('--current-row', currentRow);
    $: liElement && liElement.style.setProperty('--current-col', currentCol);
    $: liElement && liElement.style.setProperty('--initial-row', initialRow);
    $: liElement && liElement.style.setProperty('--initial-col', initialCol);

    export function canMoveTo(row, col) {
        const deltaX = Math.abs(currentCol - col);
        const deltaY = Math.abs(currentRow -  row);
        const delta = deltaX + deltaY;
        return delta === 1;
    }

    export function moveTo(row, col) {
        currentRow = row;
        currentCol = col;
    }

    export function isInRightPlace() {
        return currentRow === initialRow
            && currentCol === initialCol;
    }
</script>

<svelte:options accessors={true}/>

<li class:blank={isBlank}
    on:click
    bind:this={liElement}>
</li>

<style>
li {
  width: calc(var(--content-size) / var(--grid-size));
  height: calc(var(--content-size) / var(--grid-size));
  cursor: pointer;
  box-shadow: 0 5px 5px -3px rgba(0, 0, 0, 0.2),
              0 8px 10px 1px rgba(0, 0, 0, 0.14),
              0 3px 14px 2px rgba(0, 0, 0, 0.12);
  grid-row: calc(var(--current-row) + 1);
  grid-column: calc(var(--current-col) + 1);
  background-image: var(--image);
  background-repeat: no-repeat;
  background-size: var(--content-size);
  background-position-y: calc(var(--initial-row) * var(--content-size) / var(--grid-size) * -1);
  background-position-x: calc(var(--initial-col) * var(--content-size) / var(--grid-size) * -1);
}

li.blank {
  visibility: hidden;
  cursor: default;
  background: none;
}
</style>