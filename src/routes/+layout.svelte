<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';
	import '../app.css'
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<nav class="flex justify-between items-center p-4 bg-card shadow">

  <h1 class="text-xl font-bold text-accent">
    WatchLog
  </h1>

  <div class="flex gap-6 text-textMuted">
    <a href="/">Home</a>
    <a href="/anime">Anime</a>
    <a href="/movies">Movies</a>
    <a href="/drama">Drama</a>
  </div>

</nav>
{@render children()}
