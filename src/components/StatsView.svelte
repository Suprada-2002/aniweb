<script>
  import { BarChart3, PieChart, Star, Film, Award, Sparkles, Shuffle, Flame } from 'lucide-svelte';

  let { 
    animeList = [], 
    onSelectAnime = () => {}, 
    onTagClick = () => {} 
  } = $props();

  // Genre breakdown
  let genreCounts = $derived.by(() => {
    const map = {};
    for (const a of animeList) {
      if (a.genre && Array.isArray(a.genre)) {
        for (const g of a.genre) {
          map[g] = (map[g] || 0) + 1;
        }
      }
    }
    return Object.entries(map)
      .map(([genre, count]) => ({ genre, count }))
      .sort((a, b) => b.count - a.count);
  });

  let maxGenreCount = $derived(
    genreCounts.length > 0 ? genreCounts[0].count : 1
  );

  // Status breakdown
  let completedCount = $derived(
    animeList.filter(a => a.status.toLowerCase().includes('completed')).length
  );
  let ongoingCount = $derived(
    animeList.filter(a => a.status.toLowerCase().includes('ongoing')).length
  );
  let notWatchedCount = $derived(
    animeList.filter(a => a.status.toLowerCase().includes('not watched')).length
  );

  // Movies vs TV Series
  let moviesCount = $derived(
    animeList.filter(a => (a.type || '').toLowerCase() === 'movie').length
  );
  let tvCount = $derived(
    animeList.length - moviesCount
  );

  // Top rated
  let topRated = $derived(
    [...animeList]
      .filter(a => a.score)
      .sort((a, b) => b.score - a.score)
      .slice(0, 5)
  );

  // Random picker state
  let randomPick = $state(null);
  let isRolling = $state(false);

  function pickRandom() {
    if (animeList.length === 0) return;
    isRolling = true;
    let iterations = 0;
    const interval = setInterval(() => {
      const idx = Math.floor(Math.random() * animeList.length);
      randomPick = animeList[idx];
      iterations++;
      if (iterations > 12) {
        clearInterval(interval);
        isRolling = false;
      }
    }, 60);
  }
</script>

<div class="stats-section">
  <div class="container">
    <!-- Header -->
    <div class="stats-banner">
      <div class="banner-badge">
        <BarChart3 size={14} />
        <span>ARCHIVE INSIGHTS & ANALYTICS</span>
      </div>
      <h2 class="banner-title">Journal <span class="gradient-text">Statistics</span></h2>
      <p class="banner-sub">
        Deep dive into your viewing habits, favorite genres, top ratings, and completion rates.
      </p>
    </div>

    <!-- Analytics Top Grid -->
    <div class="analytics-grid">
      <!-- Genre Distribution Breakdown -->
      <div class="analytics-card genres-card">
        <div class="card-head">
          <div class="head-left">
            <span class="icon-purple"><PieChart size={18} /></span>
            <h3>Genre Breakdown</h3>
          </div>
          <span class="sub-label">{genreCounts.length} Distinct Genres</span>
        </div>

        <div class="genre-bars-list">
          {#each genreCounts.slice(0, 10) as item}
            {@const pct = Math.round((item.count / animeList.length) * 100)}
            <div 
              class="genre-bar-item" 
              onclick={() => onTagClick(item.genre)} 
              role="button" 
              tabindex="0"
              onkeydown={(e) => (e.key === 'Enter' || e.key === ' ') && onTagClick(item.genre)}
              title="Filter by #{item.genre}"
            >
              <div class="genre-bar-labels">
                <span class="genre-name">#{item.genre}</span>
                <span class="genre-count">{item.count} titles ({pct}%)</span>
              </div>
              <div class="bar-track">
                <div 
                  class="bar-fill" 
                  style="width: {(item.count / maxGenreCount) * 100}%"
                ></div>
              </div>
            </div>
          {/each}
        </div>
      </div>

      <!-- Right Column: Formats & Top Rated -->
      <div class="right-stats-col">
        <!-- Media Format Ratio Card -->
        <div class="analytics-card format-card">
          <div class="card-head">
            <div class="head-left">
              <span class="icon-red"><Film size={18} /></span>
              <h3>Format Distribution</h3>
            </div>
            <span class="sub-label">TV vs Movies</span>
          </div>

          <div class="format-meters">
            <div class="format-stat">
              <span class="format-val">{tvCount}</span>
              <span class="format-name">TV Series</span>
              <span class="format-pct">{Math.round((tvCount / animeList.length) * 100)}%</span>
            </div>

            <div class="format-divider"></div>

            <div class="format-stat">
              <span class="format-val">{moviesCount}</span>
              <span class="format-name">Feature Films</span>
              <span class="format-pct">{Math.round((moviesCount / animeList.length) * 100)}%</span>
            </div>
          </div>
        </div>

        <!-- Top Rated Hall of Fame -->
        <div class="analytics-card top-card">
          <div class="card-head">
            <div class="head-left">
              <span class="icon-amber"><Award size={18} /></span>
              <h3>Hall of Fame (Highest Rated)</h3>
            </div>
            <span class="sub-label">Top 5</span>
          </div>

          <div class="top-list">
            {#each topRated as anime, idx}
              <div 
                class="top-item" 
                onclick={() => onSelectAnime(anime)}
                role="button"
                tabindex="0"
                onkeydown={(e) => (e.key === 'Enter' || e.key === ' ') && onSelectAnime(anime)}
              >
                <span class="rank-num">0{idx + 1}</span>
                <div class="top-info">
                  <span class="top-title">{anime.name}</span>
                  <div class="top-tags">
                    {#if anime.genre && anime.genre[0]}
                      <span class="top-genre">#{anime.genre[0]}</span>
                    {/if}
                    <span class="top-type">{anime.type || 'TV'}</span>
                  </div>
                </div>
                <div class="top-score">
                  <span class="star-gold"><Star size={13} fill="#fbbf24" /></span>
                  <span>{anime.score}</span>
                </div>
              </div>
            {/each}
          </div>
        </div>
      </div>
    </div>

    <!-- Random Generator Interactive Banner -->
    <div class="generator-banner">
      <div class="gen-left">
        <div class="gen-icon">
          <Sparkles size={24} />
        </div>
        <div class="gen-text">
          <h3 class="gen-title">Indecisive? Let Destiny Pick</h3>
          <p class="gen-sub">Randomly draws a recommendation from your 48 tracked anime.</p>
        </div>
      </div>

      <div class="gen-right">
        {#if randomPick}
          <div 
            class="picked-preview {isRolling ? 'rolling' : ''}" 
            onclick={() => onSelectAnime(randomPick)}
            role="button"
            tabindex="0"
            onkeydown={(e) => (e.key === 'Enter' || e.key === ' ') && onSelectAnime(randomPick)}
          >
            <span class="picked-name">{randomPick.name}</span>
            <span class="picked-cta">View Details →</span>
          </div>
        {/if}

        <button class="roll-btn" onclick={pickRandom} disabled={isRolling}>
          <Shuffle size={16} />
          <span>{isRolling ? 'Choosing...' : 'Roll Next Anime'}</span>
        </button>
      </div>
    </div>
  </div>
</div>

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  .stats-section {
    padding: 2rem 0 3rem;
  }

  .stats-banner {
    @include glass-panel(20px, rgba(16, 17, 24, 0.8), rgba(255, 255, 255, 0.08));
    border-radius: $radius-xl;
    padding: 2rem;
    margin-bottom: 1.5rem;

    @include mobile {
      padding: 1.25rem;
    }

    .banner-badge {
      display: inline-flex;
      align-items: center;
      gap: 0.45rem;
      font-size: 0.72rem;
      font-weight: 700;
      letter-spacing: 0.1em;
      color: $accent-red;
      background: rgba(255, 51, 102, 0.1);
      border: 1px solid rgba(255, 51, 102, 0.3);
      padding: 0.25rem 0.65rem;
      border-radius: $radius-full;
      margin-bottom: 0.65rem;
      font-family: $font-mono;
    }

    .banner-title {
      font-size: 2rem;
      font-weight: 800;
      color: #fff;
      margin-bottom: 0.4rem;

      @include mobile {
        font-size: 1.5rem;
      }

      .gradient-text {
        background: linear-gradient(135deg, #ff3366 0%, #c084fc 100%);
        -webkit-background-clip: text;
        -webkit-text-fill-color: transparent;
      }
    }

    .banner-sub {
      font-size: 0.9rem;
      color: $text-secondary;
      max-width: 580px;
    }
  }

  .analytics-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1.5rem;
    margin-bottom: 1.5rem;

    @include tablet {
      grid-template-columns: 1fr;
    }
  }

  .analytics-card {
    @include glass-panel(16px, rgba(17, 18, 25, 0.75), rgba(255, 255, 255, 0.07));
    border-radius: $radius-xl;
    padding: 1.5rem;
    display: flex;
    flex-direction: column;
    gap: 1.25rem;

    @include mobile {
      padding: 1.15rem;
    }

    .card-head {
      @include flex-between;
      border-bottom: 1px solid rgba(255, 255, 255, 0.06);
      padding-bottom: 0.75rem;

      .head-left {
        display: flex;
        align-items: center;
        gap: 0.5rem;

        h3 {
          font-size: 1.1rem;
          font-weight: 700;
          color: #fff;
        }

        .icon-purple { color: $accent-purple; display: flex; }
        .icon-red { color: $accent-red; display: flex; }
        .icon-amber { color: $accent-amber; display: flex; }
      }

      .sub-label {
        font-size: 0.75rem;
        font-family: $font-mono;
        color: $text-muted;
      }
    }
  }

  .genre-bars-list {
    display: flex;
    flex-direction: column;
    gap: 0.85rem;

    .genre-bar-item {
      display: flex;
      flex-direction: column;
      gap: 0.35rem;
      cursor: pointer;
      padding: 0.2rem 0;
      transition: all $transition-fast;

      &:hover {
        .genre-name {
          color: $accent-red;
        }
        .bar-fill {
          background: linear-gradient(90deg, #ff3366 0%, #c084fc 100%);
        }
      }

      .genre-bar-labels {
        @include flex-between;
        font-size: 0.82rem;

        .genre-name {
          font-weight: 600;
          color: #fff;
          transition: color $transition-fast;
        }

        .genre-count {
          color: $text-muted;
          font-family: $font-mono;
          font-size: 0.75rem;
        }
      }

      .bar-track {
        height: 6px;
        background: rgba(255, 255, 255, 0.05);
        border-radius: $radius-full;
        overflow: hidden;
      }

      .bar-fill {
        height: 100%;
        background: linear-gradient(90deg, rgba(255, 51, 102, 0.6) 0%, rgba(139, 92, 246, 0.8) 100%);
        border-radius: $radius-full;
        transition: width 0.6s ease;
      }
    }
  }

  .right-stats-col {
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
  }

  .format-meters {
    display: flex;
    align-items: center;
    justify-content: space-around;
    padding: 0.5rem 0;

    .format-stat {
      display: flex;
      flex-direction: column;
      align-items: center;

      .format-val {
        font-size: 2rem;
        font-weight: 800;
        color: #fff;
        font-family: $font-mono;
      }

      .format-name {
        font-size: 0.82rem;
        color: $text-secondary;
        font-weight: 600;
        margin-top: 0.2rem;
      }

      .format-pct {
        font-size: 0.72rem;
        color: $text-muted;
        font-family: $font-mono;
      }
    }

    .format-divider {
      width: 1px;
      height: 50px;
      background: rgba(255, 255, 255, 0.08);
    }
  }

  .top-list {
    display: flex;
    flex-direction: column;
    gap: 0.65rem;

    .top-item {
      display: flex;
      align-items: center;
      gap: 0.85rem;
      padding: 0.65rem 0.85rem;
      border-radius: $radius-md;
      background: rgba(255, 255, 255, 0.02);
      border: 1px solid rgba(255, 255, 255, 0.05);
      cursor: pointer;
      transition: all $transition-fast;

      &:hover {
        background: rgba(255, 255, 255, 0.06);
        border-color: rgba(255, 255, 255, 0.15);
        transform: translateX(4px);
      }

      .rank-num {
        font-size: 0.85rem;
        font-weight: 800;
        color: $accent-red;
        font-family: $font-mono;
      }

      .top-info {
        flex: 1;
        display: flex;
        flex-direction: column;
        gap: 0.2rem;

        .top-title {
          font-size: 0.88rem;
          font-weight: 700;
          color: #fff;
        }

        .top-tags {
          display: flex;
          align-items: center;
          gap: 0.4rem;
          font-size: 0.7rem;

          .top-genre {
            color: #c084fc;
          }

          .top-type {
            color: $text-muted;
          }
        }
      }

      .top-score {
        display: flex;
        align-items: center;
        gap: 0.25rem;
        font-size: 0.85rem;
        font-weight: 700;
        font-family: $font-mono;
        color: #fff;

        .star-gold {
          color: #fbbf24;
          display: flex;
        }
      }
    }
  }

  .generator-banner {
    @include glass-panel(20px, rgba(20, 22, 32, 0.8), rgba(255, 51, 102, 0.25));
    border-radius: $radius-xl;
    padding: 1.5rem 2rem;
    @include flex-between;
    gap: 1.5rem;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);

    @include tablet {
      flex-direction: column;
      align-items: stretch;
      gap: 1.25rem;
    }

    @include mobile {
      padding: 1.25rem;
    }

    .gen-left {
      display: flex;
      align-items: center;
      gap: 1rem;

      .gen-icon {
        width: 48px;
        height: 48px;
        border-radius: $radius-md;
        background: linear-gradient(135deg, $accent-red 0%, $accent-purple 100%);
        @include flex-center;
        color: #fff;
        flex-shrink: 0;
        box-shadow: 0 0 20px $accent-red-glow;
      }

      .gen-title {
        font-size: 1.15rem;
        font-weight: 800;
        color: #fff;
      }

      .gen-sub {
        font-size: 0.85rem;
        color: $text-secondary;
      }
    }

    .gen-right {
      display: flex;
      align-items: center;
      gap: 1rem;

      @include mobile {
        flex-direction: column;
        align-items: stretch;
      }

      .picked-preview {
        display: flex;
        align-items: center;
        gap: 0.75rem;
        background: rgba(255, 255, 255, 0.06);
        border: 1px solid rgba(255, 255, 255, 0.15);
        padding: 0.65rem 1rem;
        border-radius: $radius-lg;
        cursor: pointer;
        transition: all $transition-fast;

        &.rolling {
          opacity: 0.6;
          filter: blur(1px);
        }

        &:hover {
          background: rgba(255, 255, 255, 0.12);
          border-color: $accent-red;
        }

        .picked-name {
          font-size: 0.9rem;
          font-weight: 700;
          color: #fff;
        }

        .picked-cta {
          font-size: 0.75rem;
          color: $accent-red;
          font-weight: 600;
        }
      }

      .roll-btn {
        display: inline-flex;
        align-items: center;
        justify-content: center;
        gap: 0.5rem;
        background: linear-gradient(135deg, $accent-red 0%, #b31238 100%);
        color: #fff;
        padding: 0.75rem 1.35rem;
        border-radius: $radius-lg;
        font-weight: 700;
        font-size: 0.88rem;
        box-shadow: 0 0 20px $accent-red-glow;
        transition: all $transition-normal;
        white-space: nowrap;

        &:hover:not(:disabled) {
          transform: translateY(-2px);
          box-shadow: 0 0 25px rgba(255, 51, 102, 0.6);
        }

        &:disabled {
          opacity: 0.65;
          cursor: not-allowed;
        }
      }
    }
  }
</style>
