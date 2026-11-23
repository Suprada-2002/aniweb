<script>
  import { Film, Target, Sparkles } from 'lucide-svelte';

  let { activeTab = 'anime', onTabChange = () => {}, animeCount = 0, goalsCount = 0 } = $props();
</script>

<header class="navbar">
  <div class="container nav-content">
    <div class="brand">
      <div class="logo-icon">
        <Sparkles size={18} class="sparkle-icon" />
      </div>
      <div class="brand-text">
        <div class="brand-title">
          <span>My Journel</span>
        </div>
        <span class="brand-sub">ANIME & LIFE LOG</span>
      </div>
    </div>

    <nav class="nav-links">
      <button 
        class="nav-tab {activeTab === 'anime' ? 'active' : ''}"
        onclick={() => onTabChange('anime')}
      >
        <Film size={16} />
        <span>Anime Log</span>
        <span class="counter-badge">{animeCount}</span>
      </button>

      <button 
        class="nav-tab {activeTab === 'goals' ? 'active' : ''}"
        onclick={() => onTabChange('goals')}
      >
        <Target size={16} />
        <span>Life Goals</span>
        <span class="counter-badge goals-badge">{goalsCount}</span>
      </button>
    </nav>
  </div>
</header>

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  .navbar {
    position: sticky;
    top: 0;
    z-index: 100;
    @include glass-panel(20px, rgba(10, 10, 14, 0.85), rgba(255, 255, 255, 0.08));
    border-top: none;
    border-left: none;
    border-right: none;
    padding: 0.85rem 0;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
  }

  .nav-content {
    @include flex-between;
    gap: 1.5rem;

    @include mobile {
      flex-direction: column;
      align-items: stretch;
      gap: 1rem;
    }
  }

  .brand {
    display: flex;
    align-items: center;
    gap: 0.85rem;

    @include mobile {
      justify-content: center;
    }

    .logo-icon {
      width: 38px;
      height: 38px;
      border-radius: $radius-md;
      background: linear-gradient(135deg, $accent-red 0%, #b31238 100%);
      @include flex-center;
      color: #fff;
      box-shadow: 0 0 20px $accent-red-glow;
      transition: transform $transition-bounce;

      &:hover {
        transform: rotate(10deg) scale(1.05);
      }
    }

    .brand-title {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      font-size: 1.25rem;
      font-weight: 800;
      letter-spacing: 0.08em;
      color: #fff;
    }

    .brand-sub {
      font-size: 0.65rem;
      letter-spacing: 0.18em;
      color: $text-muted;
      font-weight: 600;
      text-transform: uppercase;
      font-family: $font-mono;
    }
  }

  .nav-links {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    background: rgba(255, 255, 255, 0.03);
    padding: 0.3rem;
    border-radius: $radius-full;
    border: 1px solid rgba(255, 255, 255, 0.06);

    @include mobile {
      justify-content: center;
      width: 100%;
    }
  }

  .nav-tab {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    padding: 0.5rem 1rem;
    border-radius: $radius-full;
    font-size: 0.85rem;
    font-weight: 600;
    color: $text-secondary;
    transition: all $transition-normal;
    position: relative;

    @include mobile {
      flex: 1;
      justify-content: center;
      padding: 0.5rem 0.6rem;
      font-size: 0.8rem;
    }

    &:hover {
      color: #fff;
      background: rgba(255, 255, 255, 0.06);
    }

    &.active {
      color: #fff;
      background: linear-gradient(135deg, rgba(255, 51, 102, 0.22) 0%, rgba(139, 92, 246, 0.15) 100%);
      border: 1px solid rgba(255, 51, 102, 0.45);
      box-shadow: 0 0 15px rgba(255, 51, 102, 0.2);

      .counter-badge {
        background: $accent-red;
        color: #fff;
      }

      .counter-badge.goals-badge {
        background: $accent-purple;
      }
    }

    .counter-badge {
      font-size: 0.7rem;
      font-weight: 700;
      padding: 0.1rem 0.45rem;
      border-radius: $radius-full;
      background: rgba(255, 255, 255, 0.1);
      color: $text-secondary;
      transition: all $transition-normal;
      font-family: $font-mono;
    }
  }
</style>
