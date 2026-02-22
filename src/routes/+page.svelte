<!-- <script>
    import {animeData} from '$lib/data/animeData'
</script>

<h1 class="header">Log of all the watched Animes</h1>

<div class="anime-grid">
    {#each animeData as data}
        <div class="anime-card">
            <h2>{data.name}</h2>
            <span>{data.status}</span>
        </div>
    {/each}
</div> -->
<script>
  import Section from "$lib/assets/Section.svelte";
  import { animeData } from "$lib/data/animeData";
  // import { movieData } from "$lib/data/movieData";
  // import { dramaData } from "$lib/data/dramaData";
</script>

<div class="min-h-screen bg-bg p-8">

  <h1 class="text-4xl font-bold text-center text-accent mb-10">
    🎬 My Watch Journal
  </h1>

  <Section title="Anime" items={animeData} />
  <!-- <Section title="Movies" items={movieData} />
  <Section title="Drama" items={dramaData} /> -->

</div>
