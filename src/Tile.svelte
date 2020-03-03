<script>
export let col;
export let row;
export let colStart;
export let rowStart;

$: element.style.setProperty('--row', row);
$: element.style.setProperty('--col', col);
$: element.style.setProperty('--col-start', colStart);
$: element.style.setProperty('--col-start', rowStart);
</script>