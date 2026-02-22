<script>
  export let item;
</script>

<div
  class="group relative bg-card rounded-2xl overflow-hidden border border-app
         transition duration-300 hover:scale-[1.03]
         hover:shadow-[0_0_25px_rgba(124,58,237,0.35)]"
>

  <!-- Poster -->
  <div class="relative">
    <img
      src={item.url}
      alt={item.name}
      class="h-64 w-full object-cover"
      loading="lazy"
    />

    <!-- Gradient Overlay -->
    <div
      class="absolute inset-0 bg-gradient-to-t from-black/80 via-black/20 to-transparent"
    ></div>

    <!-- Status Badge -->
    <div class="absolute top-3 left-3">
      <span
        class="px-2 py-1 text-xs rounded-md bg-primary/80 text-white backdrop-blur"
      >
        {item.status}
      </span>
    </div>

    <!-- Rating -->
    {#if item.rating}
      <div class="absolute top-3 right-3">
        <span
          class="px-2 py-1 text-xs rounded-md bg-black/60 text-yellow-300 backdrop-blur"
        >
          ⭐ {item.rating}
        </span>
      </div>
    {/if}
  </div>

  <!-- Content -->
  <div class="p-4">

    <h2 class="text-lg font-semibold text-main line-clamp-1">
      {item.name}
    </h2>

    <!-- Genres -->
    <div class="flex flex-wrap gap-2 mt-2">
      {#each item.genre as g}
        <span
          class="px-2 py-1 text-xs rounded-md
                 bg-[rgba(124,58,237,0.15)]
                 text-secondary
                 border border-[rgba(124,58,237,0.25)]"
        >
          {g}
        </span>
      {/each}
    </div>

  </div>

</div>