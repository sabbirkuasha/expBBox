<script>
	import { onMount } from 'svelte';

	let divElement;
	let divHeight;
	$: console.log(divHeight);
</script>

<div class="w-20 h-20 border" bind:this={divElement} bind:clientHeight={divHeight} />
