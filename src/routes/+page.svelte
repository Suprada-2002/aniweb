<script>
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
</div>

<style>
.anime-grid {
  display: grid;
  grid-template-columns: 1fr;   
  gap: 1.25rem;      
  max-width: 1400px; 
  margin: 0 auto;         
}

.anime-card{
    border: 1px solid black; 
}

.header{
 text-align: center;
}

/* tablet */
@media (min-width: 768px) {
  .anime-grid {
    grid-template-columns: repeat(3, 1fr);
  }
}

/* desktop */
@media (min-width: 1024px) {
  .anime-grid {
    grid-template-columns: repeat(5, 1fr);
  }
}

</style>