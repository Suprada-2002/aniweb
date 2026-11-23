<script>
  import { CheckCircle2, Clock, EyeOff, Sparkles, Shuffle, Flame } from 'lucide-svelte';

  let { 
    totalCount = 0, 
    completedCount = 0, 
    ongoingCount = 0, 
    notWatchedCount = 0,
    onRandomPick = () => {}
  } = $props();

  let completionRate = $derived(
    totalCount > 0 ? Math.round((completedCount / totalCount) * 100) : 0
  );
</script>

<div class="hero-section">
  <div class="hero-bg-glow"></div>
  <div class="container hero-container">
    <div class="hero-header">
      <div class="header-left">
        <div class="meta-tag">
          <Flame size={14} class="flame-icon" />
          <span>PERSONAL CHRONICLES & LOGBOOK</span>
        </div>
        <h1 class="hero-title">
          Anime & Shows <span class="gradient-text">Archive</span>
        </h1>
        <p class="hero-desc">
          Tracking a curated collection of {totalCount} anime, movies, and ongoing broadcasts. Fast, static, and immortalized.
        </p>
      </div>

      <div class="header-actions">
        <button class="random-btn" onclick={onRandomPick} title="Pick a random anime from your list">
          <Shuffle size={16} />
          <span>Surprise Pick</span>
        </button>
      </div>
    </div>

    <!-- Stats Bar Cards -->
    <div class="stats-grid">
      <div class="stat-card total-card">
        <div class="stat-icon-wrapper">
          <Sparkles size={20} />
        </div>
        <div class="stat-data">
          <span class="stat-value">{totalCount}</span>
          <span class="stat-label">Total Tracked</span>
        </div>
      </div>

      <div class="stat-card completed-card">
        <div class="stat-icon-wrapper completed-icon">
          <CheckCircle2 size={20} />
        </div>
        <div class="stat-data">
          <span class="stat-value">{completedCount}</span>
          <span class="stat-label">Completed</span>
        </div>
      </div>

      <div class="stat-card ongoing-card">
        <div class="stat-icon-wrapper ongoing-icon">
          <Clock size={20} />
        </div>
        <div class="stat-data">
          <span class="stat-value">{ongoingCount}</span>
          <span class="stat-label">Ongoing / Watching</span>
        </div>
      </div>

      <div class="stat-card backlog-card">
        <div class="stat-icon-wrapper backlog-icon">
          <EyeOff size={20} />
        </div>
        <div class="stat-data">
          <span class="stat-value">{notWatchedCount}</span>
          <span class="stat-label">Plan to Watch</span>
        </div>
      </div>
    </div>

    <!-- Progress Tracker Strip -->
    <div class="progress-bar-wrapper">
      <div class="progress-header">
        <span class="progress-title">Series Completion Rate</span>
        <span class="progress-pct">{completionRate}% Finished</span>
      </div>
      <div class="progress-track">
        <div 
          class="progress-fill completed-fill" 
          style="width: {completionRate}%"
          title="Completed: {completedCount}"
        ></div>
        <div 
          class="progress-fill ongoing-fill" 
          style="width: {totalCount > 0 ? (ongoingCount / totalCount) * 100 : 0}%"
          title="Ongoing: {ongoingCount}"
        ></div>
        <div 
          class="progress-fill backlog-fill" 
          style="width: {totalCount > 0 ? (notWatchedCount / totalCount) * 100 : 0}%"
          title="Not Watched: {notWatchedCount}"
        ></div>
      </div>
      <div class="progress-legend">
        <div class="legend-item"><span class="dot dot-completed"></span> Completed ({completedCount})</div>
        <div class="legend-item"><span class="dot dot-ongoing"></span> Ongoing ({ongoingCount})</div>
        <div class="legend-item"><span class="dot dot-backlog"></span> Not Watched ({notWatchedCount})</div>
      </div>
    </div>
  </div>
</div>

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  .hero-section {
    position: relative;
    padding: 2.5rem 0 1.5rem;
    overflow: hidden;

    @include mobile {
      padding: 1.75rem 0 1rem;
    }
  }

  .hero-bg-glow {
    position: absolute;
    top: -80px;
    left: 50%;
    transform: translateX(-50%);
    width: 600px;
    height: 250px;
    background: radial-gradient(ellipse, rgba(255, 51, 102, 0.12) 0%, rgba(139, 92, 246, 0.05) 50%, transparent 80%);
    filter: blur(50px);
    pointer-events: none;
    z-index: 0;
  }

  .hero-container {
    position: relative;
    z-index: 1;
    display: flex;
    flex-direction: column;
    gap: 1.75rem;
  }

  .hero-header {
    @include flex-between;
    gap: 1.5rem;

    @include mobile {
      flex-direction: column;
      align-items: flex-start;
      gap: 1.25rem;
    }
  }

  .meta-tag {
    display: inline-flex;
    align-items: center;
    gap: 0.45rem;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.12em;
    color: $accent-red;
    background: rgba(255, 51, 102, 0.08);
    border: 1px solid rgba(255, 51, 102, 0.25);
    padding: 0.25rem 0.65rem;
    border-radius: $radius-full;
    margin-bottom: 0.6rem;
    font-family: $font-mono;
  }

  .hero-title {
    font-size: 2.25rem;
    font-weight: 800;
    letter-spacing: -0.02em;
    line-height: 1.15;
    margin-bottom: 0.4rem;
    color: #fff;

    @include mobile {
      font-size: 1.75rem;
    }

    .gradient-text {
      background: linear-gradient(135deg, #ff3366 0%, #ff80a0 50%, #c084fc 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
  }

  .hero-desc {
    color: $text-secondary;
    font-size: 0.95rem;
    max-width: 620px;
    line-height: 1.6;

    @include mobile {
      font-size: 0.88rem;
    }
  }

  .random-btn {
    display: inline-flex;
    align-items: center;
    gap: 0.55rem;
    background: linear-gradient(135deg, rgba(255, 51, 102, 0.15) 0%, rgba(139, 92, 246, 0.15) 100%);
    border: 1px solid rgba(255, 51, 102, 0.4);
    color: #fff;
    padding: 0.75rem 1.25rem;
    border-radius: $radius-lg;
    font-weight: 600;
    font-size: 0.88rem;
    box-shadow: 0 4px 20px rgba(255, 51, 102, 0.15);
    transition: all $transition-normal;
    white-space: nowrap;

    &:hover {
      background: linear-gradient(135deg, rgba(255, 51, 102, 0.3) 0%, rgba(139, 92, 246, 0.25) 100%);
      border-color: $accent-red;
      transform: translateY(-2px);
      box-shadow: 0 6px 25px rgba(255, 51, 102, 0.3);
    }

    &:active {
      transform: translateY(0);
    }
  }

  .stats-grid {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 1rem;

    @include tablet {
      grid-template-columns: repeat(2, 1fr);
    }

    @include mobile {
      grid-template-columns: repeat(2, 1fr);
      gap: 0.75rem;
    }
  }

  .stat-card {
    @include glass-panel(16px, rgba(17, 18, 25, 0.75), rgba(255, 255, 255, 0.07));
    padding: 1.1rem 1.25rem;
    border-radius: $radius-lg;
    display: flex;
    align-items: center;
    gap: 1rem;
    transition: all $transition-normal;

    &:hover {
      transform: translateY(-3px);
      border-color: rgba(255, 255, 255, 0.18);
      background: rgba(22, 24, 33, 0.85);
    }

    .stat-icon-wrapper {
      width: 44px;
      height: 44px;
      border-radius: $radius-md;
      @include flex-center;
      background: rgba(255, 255, 255, 0.05);
      color: $text-secondary;
      flex-shrink: 0;

      &.completed-icon {
        background: rgba(16, 185, 129, 0.15);
        color: $accent-emerald;
      }

      &.ongoing-icon {
        background: rgba(245, 158, 11, 0.15);
        color: $accent-amber;
      }

      &.backlog-icon {
        background: rgba(148, 163, 184, 0.12);
        color: $text-muted;
      }
    }

    .stat-data {
      display: flex;
      flex-direction: column;

      .stat-value {
        font-size: 1.5rem;
        font-weight: 800;
        color: #fff;
        line-height: 1.1;
        font-family: $font-mono;
      }

      .stat-label {
        font-size: 0.75rem;
        color: $text-muted;
        font-weight: 500;
        margin-top: 0.2rem;
      }
    }
  }

  .progress-bar-wrapper {
    @include glass-panel(16px, rgba(14, 15, 20, 0.6), rgba(255, 255, 255, 0.05));
    border-radius: $radius-lg;
    padding: 1rem 1.25rem;
    display: flex;
    flex-direction: column;
    gap: 0.65rem;

    .progress-header {
      @include flex-between;
      font-size: 0.82rem;
      font-weight: 600;

      .progress-title {
        color: $text-secondary;
      }

      .progress-pct {
        color: $accent-emerald;
        font-family: $font-mono;
      }
    }

    .progress-track {
      height: 7px;
      background: rgba(255, 255, 255, 0.05);
      border-radius: $radius-full;
      overflow: hidden;
      display: flex;
      width: 100%;
    }

    .progress-fill {
      height: 100%;
      transition: width 0.6s cubic-bezier(0.16, 1, 0.3, 1);

      &.completed-fill {
        background: $accent-emerald;
        box-shadow: 0 0 10px rgba(16, 185, 129, 0.5);
      }

      &.ongoing-fill {
        background: $accent-amber;
        box-shadow: 0 0 10px rgba(245, 158, 11, 0.4);
      }

      &.backlog-fill {
        background: $text-muted;
      }
    }

    .progress-legend {
      display: flex;
      align-items: center;
      gap: 1.25rem;
      font-size: 0.75rem;
      color: $text-muted;

      @include mobile {
        flex-wrap: wrap;
        gap: 0.75rem;
      }

      .legend-item {
        display: flex;
        align-items: center;
        gap: 0.4rem;
      }

      .dot {
        width: 8px;
        height: 8px;
        border-radius: 50%;

        &.dot-completed { background: $accent-emerald; }
        &.dot-ongoing { background: $accent-amber; }
        &.dot-backlog { background: $text-muted; }
      }
    }
  }
</style>
