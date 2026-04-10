<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';

	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<style>
  :global(body) {
  margin: 0;
  min-height: 100vh;
  background: radial-gradient(circle at top, #5b21b6, #1e0a3c);
  font-family: sans-serif;
}
</style>

{@render children()}
