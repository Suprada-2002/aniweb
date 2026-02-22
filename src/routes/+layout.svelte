<script lang="ts">
	import favicon from '$lib/assets/favicon.svg';
	import '../app.css'
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href={favicon} />
</svelte:head>

<nav
  class="sticky top-0 z-50 backdrop-blur bg-card/70 border-b border-app shadow-sm"
>
  <div class="max-w-6xl mx-auto px-6 py-4 flex items-center justify-between">
<h1
  class="text-xl md:text-2xl font-bold text-primary tracking-wide
         hover:drop-shadow-[0_0_8px_rgba(139,92,246,0.6)]
         transition"
>
  MyWatchJournel
</h1>

<!-- Links -->
<div class="flex items-center gap-6 text-sm md:text-base">

  <a
    href="/"
    class="text-muted hover:text-primary transition relative group"
  >
    Home
    <span
      class="absolute -bottom-1 left-0 w-0 h-[2px] bg-primary
             transition-all group-hover:w-full"
    ></span>
  </a>

  <a
    href="/anime"
    class="text-muted hover:text-primary transition relative group"
  >
    Know Me
    <span
      class="absolute -bottom-1 left-0 w-0 h-[2px] bg-primary
             transition-all group-hover:w-full"
    ></span>
  </a>

  <a
    href="/"
    class="text-muted hover:text-primary transition relative group"
  >
    anime
    <span
      class="absolute -bottom-1 left-0 w-0 h-[2px] bg-primary
             transition-all group-hover:w-full"
    ></span>
  </a>

  <a
    href="/"
    class="text-muted hover:text-primary transition relative group"
  >
    Drama
    <span
      class="absolute -bottom-1 left-0 w-0 h-[2px] bg-primary
             transition-all group-hover:w-full"
    ></span>
  </a>

</div>
  </div>
</nav>

{@render children()}
<footer>
	<div class="container text-center mx-auto border-t py-5">
		<p>Copy @ Suprada Tripathy - { new Date().getFullYear()}</p>
	</div>
</footer>