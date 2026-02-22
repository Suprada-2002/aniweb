<script>
  export let item;
</script>

<div class="bg-card rounded-2xl p-4 shadow-lg hover:scale-105 transition">
  
  <img
    src={item.url}
    alt={item.name}
    class="rounded-xl mb-3 h-56 w-full object-cover"
  />

  <h2 class="text-lg font-semibold text-textMain">
    {item.name}
  </h2>

  <div class="flex flex-wrap gap-2 mt-2">
    {#each item.genre as g}
      <span class="px-2 py-1 text-xs bg-accent/20 text-accentSoft rounded-md">
        {g}
      </span>
    {/each}
  </div>

  <div class="flex justify-between mt-3 text-sm text-textMuted">
    <span>{item.status}</span>
    <span>⭐ {item.rating}</span>
  </div>

</div>