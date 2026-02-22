<script>
  import MediaCard from "./mediaCard.svelte";
  export let title;
  export let items = [];
</script>

<section class="mb-10">
  <h1 class="text-2xl font-bold text-red-300 mb-6">
    {title}
  </h1>

  <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-6">
    {#each items as item}
      <MediaCard {item} />
    {/each}
  </div>
</section>