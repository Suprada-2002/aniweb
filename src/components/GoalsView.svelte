<script>
  import { onMount } from 'svelte';
  import { Target, CheckCircle2, Circle, Trophy, Flame, Plus, Check, Clock, AlertCircle } from 'lucide-svelte';

  let { initialGoals = [] } = $props();

  let goals = $state([]);

  onMount(() => {
    try {
      const saved = localStorage.getItem('aniweb_goals_data');
      if (saved) {
        goals = JSON.parse(saved);
      } else {
        goals = JSON.parse(JSON.stringify(initialGoals));
      }
    } catch (e) {
      goals = JSON.parse(JSON.stringify(initialGoals));
    }
  });

  function saveGoals() {
    try {
      localStorage.setItem('aniweb_goals_data', JSON.stringify(goals));
    } catch (e) {
      console.error('Failed to save goals', e);
    }
  }

  function toggleLevel(goalIndex, levelIndex) {
    goals[goalIndex].levels[levelIndex].status = !goals[goalIndex].levels[levelIndex].status;
    
    // Auto-update goal status based on levels
    const totalLevels = goals[goalIndex].levels.length;
    if (totalLevels > 0) {
      const completedLevels = goals[goalIndex].levels.filter(l => l.status).length;
      if (completedLevels === totalLevels) {
        goals[goalIndex].status = 'Completed';
      } else if (completedLevels > 0) {
        goals[goalIndex].status = 'Ongoing';
      } else {
        goals[goalIndex].status = 'Ongoing';
      }
    }
    saveGoals();
  }

  let totalGoals = $derived(goals.length);
  let completedGoals = $derived(goals.filter(g => g.status.toLowerCase() === 'completed').length);
  let ongoingGoals = $derived(goals.filter(g => g.status.toLowerCase().includes('ongoing')).length);

  let totalLevelsCount = $derived(
    goals.reduce((acc, g) => acc + (g.levels ? g.levels.length : 0), 0)
  );

  let completedLevelsCount = $derived(
    goals.reduce((acc, g) => acc + (g.levels ? g.levels.filter(l => l.status).length : 0), 0)
  );

  let overallPercent = $derived(
    totalLevelsCount > 0 ? Math.round((completedLevelsCount / totalLevelsCount) * 100) : 0
  );

  function resetGoals() {
    if (confirm('Reset goals to default static data?')) {
      goals = JSON.parse(JSON.stringify(initialGoals));
      saveGoals();
    }
  }
</script>

<div class="goals-section">
  <div class="container">
    <!-- Header Banner -->
    <div class="goals-banner">
      <div class="banner-content">
        <div class="banner-badge">
          <Trophy size={14} />
          <span>LIFE SKILLS & MASTERY LOGBOOK</span>
        </div>
        <h2 class="banner-title">Personal <span class="gradient-text">Goals & Milestones</span></h2>
        <p class="banner-sub">
          Tracking skills from Rubik's cubes, skateboarding, to flexibility and chess. Check off sub-levels as you progress.
        </p>
      </div>

      <div class="banner-stats">
        <div class="stat-pill">
          <span class="pill-num">{completedGoals} / {totalGoals}</span>
          <span class="pill-label">Goals Mastered</span>
        </div>
        <div class="stat-pill">
          <span class="pill-num">{completedLevelsCount} / {totalLevelsCount}</span>
          <span class="pill-label">Milestones Reached</span>
        </div>
        <div class="stat-pill highlight-pill">
          <span class="pill-num">{overallPercent}%</span>
          <span class="pill-label">Total Completion</span>
        </div>
      </div>
    </div>

    <!-- Overall Progress Line -->
    <div class="overall-progress-card">
      <div class="progress-bar-header">
        <span>Skill Milestones Mastery</span>
        <span class="percent-label">{overallPercent}% Done</span>
      </div>
      <div class="track-bar">
        <div class="fill-bar" style="width: {overallPercent}%"></div>
      </div>
    </div>

    <!-- Goals Grid -->
    <div class="goals-grid">
      {#each goals as goal, gIdx}
        {@const completedCount = goal.levels ? goal.levels.filter(l => l.status).length : 0}
        {@const totalCount = goal.levels ? goal.levels.length : 0}
        {@const percent = totalCount > 0 ? Math.round((completedCount / totalCount) * 100) : 0}
        {@const isDone = goal.status.toLowerCase() === 'completed' || (totalCount > 0 && completedCount === totalCount)}

        <div class="goal-card {isDone ? 'card-completed' : ''}">
          <div class="goal-header">
            <div class="goal-title-group">
              <span class="goal-id">GOAL #{goal.id}</span>
              {#if goal.category}
                <span class="goal-category">{goal.category}</span>
              {/if}
              <h3 class="goal-name">{goal.name}</h3>
            </div>

            <div class="goal-status-badge {isDone ? 'badge-completed' : 'badge-ongoing'}">
              {#if isDone}
                <CheckCircle2 size={13} />
                <span>Completed</span>
              {:else}
                <Clock size={13} />
                <span>{goal.status}</span>
              {/if}
            </div>
          </div>

          {#if goal.description}
            <p class="goal-desc">{goal.description}</p>
          {/if}

          <!-- Goal Progress Mini Bar -->
          <div class="mini-progress">
            <div class="mini-header">
              <span class="mini-title">Milestones</span>
              <span class="mini-count">{completedCount} of {totalCount} completed ({percent}%)</span>
            </div>
            <div class="mini-track">
              <div class="mini-fill" style="width: {percent}%"></div>
            </div>
          </div>

          <!-- Sub-level Checklist -->
          <div class="levels-checklist">
            {#if goal.levels && goal.levels.length > 0}
              {#each goal.levels as level, lIdx}
                <button 
                  class="level-item {level.status ? 'level-done' : ''}"
                  onclick={() => toggleLevel(gIdx, lIdx)}
                  title="Click to toggle completion"
                >
                  <div class="checkbox-indicator">
                    {#if level.status}
                      <span class="check-icon"><Check size={14} /></span>
                    {:else}
                      <span class="circle-icon"><Circle size={14} /></span>
                    {/if}
                  </div>

                  <div class="level-info">
                    <span class="level-name">{level.name}</span>
                    {#if level.notes}
                      <span class="level-notes">{level.notes}</span>
                    {/if}
                  </div>
                </button>
              {/each}
            {:else}
              <div class="no-levels-notice">
                <AlertCircle size={14} />
                <span>No sub-milestones defined yet. In progress!</span>
              </div>
            {/if}
          </div>
        </div>
      {/each}
    </div>

    <!-- Reset to static data footer button -->
    <div class="goals-footer">
      <button class="reset-goals-btn" onclick={resetGoals}>
        Reset Goals to Static Defaults
      </button>
    </div>
  </div>
</div>

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  .goals-section {
    padding: 2rem 0 3rem;
  }

  .goals-banner {
    @include glass-panel(20px, rgba(16, 17, 24, 0.8), rgba(255, 255, 255, 0.08));
    border-radius: $radius-xl;
    padding: 2rem;
    @include flex-between;
    gap: 2rem;
    margin-bottom: 1.5rem;
    position: relative;
    overflow: hidden;

    &::after {
      content: '';
      position: absolute;
      top: -50%;
      right: -10%;
      width: 300px;
      height: 300px;
      background: radial-gradient(circle, rgba(139, 92, 246, 0.15) 0%, transparent 70%);
      pointer-events: none;
    }

    @include tablet {
      flex-direction: column;
      align-items: flex-start;
      gap: 1.5rem;
    }

    @include mobile {
      padding: 1.25rem;
    }
  }

  .banner-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.45rem;
    font-size: 0.72rem;
    font-weight: 700;
    letter-spacing: 0.1em;
    color: $accent-purple;
    background: rgba(139, 92, 246, 0.12);
    border: 1px solid rgba(139, 92, 246, 0.3);
    padding: 0.25rem 0.65rem;
    border-radius: $radius-full;
    margin-bottom: 0.65rem;
    font-family: $font-mono;
  }

  .banner-title {
    font-size: 2rem;
    font-weight: 800;
    color: #fff;
    line-height: 1.2;
    margin-bottom: 0.4rem;

    @include mobile {
      font-size: 1.5rem;
    }

    .gradient-text {
      background: linear-gradient(135deg, #c084fc 0%, #ff3366 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
    }
  }

  .banner-sub {
    font-size: 0.9rem;
    color: $text-secondary;
    max-width: 580px;
    line-height: 1.5;
  }

  .banner-stats {
    display: flex;
    gap: 1rem;

    @include mobile {
      width: 100%;
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 0.5rem;
    }

    .stat-pill {
      background: rgba(255, 255, 255, 0.04);
      border: 1px solid rgba(255, 255, 255, 0.08);
      border-radius: $radius-lg;
      padding: 0.85rem 1.15rem;
      display: flex;
      flex-direction: column;
      align-items: center;
      min-width: 110px;

      @include mobile {
        min-width: auto;
        padding: 0.65rem 0.4rem;
        text-align: center;
      }

      .pill-num {
        font-size: 1.35rem;
        font-weight: 800;
        color: #fff;
        font-family: $font-mono;

        @include mobile {
          font-size: 1.1rem;
        }
      }

      .pill-label {
        font-size: 0.7rem;
        color: $text-muted;
        margin-top: 0.2rem;
      }

      &.highlight-pill {
        background: rgba(16, 185, 129, 0.1);
        border-color: rgba(16, 185, 129, 0.3);

        .pill-num {
          color: $accent-emerald;
        }
      }
    }
  }

  .overall-progress-card {
    @include glass-panel(16px, rgba(14, 15, 22, 0.7), rgba(255, 255, 255, 0.06));
    border-radius: $radius-lg;
    padding: 1rem 1.5rem;
    margin-bottom: 2rem;

    .progress-bar-header {
      @include flex-between;
      font-size: 0.85rem;
      font-weight: 700;
      color: $text-secondary;
      margin-bottom: 0.6rem;

      .percent-label {
        color: $accent-purple;
        font-family: $font-mono;
      }
    }

    .track-bar {
      height: 9px;
      background: rgba(255, 255, 255, 0.05);
      border-radius: $radius-full;
      overflow: hidden;
    }

    .fill-bar {
      height: 100%;
      background: linear-gradient(90deg, #8b5cf6 0%, #ff3366 100%);
      box-shadow: 0 0 15px rgba(139, 92, 246, 0.5);
      transition: width 0.5s ease-out;
    }
  }

  .goals-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1.5rem;

    @include tablet {
      grid-template-columns: repeat(2, 1fr);
    }

    @include mobile {
      grid-template-columns: 1fr;
    }
  }

  .goal-card {
    @include glass-panel(16px, rgba(17, 18, 25, 0.75), rgba(255, 255, 255, 0.07));
    border-radius: $radius-xl;
    padding: 1.5rem;
    display: flex;
    flex-direction: column;
    gap: 1.15rem;
    transition: all $transition-normal;

    &:hover {
      transform: translateY(-4px);
      border-color: rgba(139, 92, 246, 0.4);
      box-shadow: 0 12px 30px rgba(0, 0, 0, 0.6), 0 0 20px rgba(139, 92, 246, 0.15);
      background: rgba(22, 24, 34, 0.9);
    }

    &.card-completed {
      border-color: rgba(16, 185, 129, 0.35);

      &:hover {
        border-color: rgba(16, 185, 129, 0.6);
        box-shadow: 0 12px 30px rgba(0, 0, 0, 0.6), 0 0 20px rgba(16, 185, 129, 0.2);
      }
    }
  }

  .goal-header {
    @include flex-between;
    align-items: flex-start;
    gap: 1rem;

    .goal-title-group {
      display: flex;
      flex-direction: column;
      gap: 0.3rem;

      .goal-id {
        font-size: 0.68rem;
        font-family: $font-mono;
        color: $text-dim;
        font-weight: 700;
      }

      .goal-category {
        display: inline-block;
        font-size: 0.68rem;
        font-weight: 600;
        color: $accent-cyan;
        background: rgba(6, 182, 212, 0.1);
        border: 1px solid rgba(6, 182, 212, 0.25);
        padding: 0.1rem 0.45rem;
        border-radius: $radius-sm;
        width: fit-content;
      }

      .goal-name {
        font-size: 1.25rem;
        font-weight: 800;
        color: #fff;
        line-height: 1.25;
      }
    }
  }

  .goal-status-badge {
    display: inline-flex;
    align-items: center;
    gap: 0.35rem;
    padding: 0.25rem 0.65rem;
    border-radius: $radius-full;
    font-size: 0.72rem;
    font-weight: 700;
    white-space: nowrap;

    &.badge-completed {
      background: rgba(16, 185, 129, 0.15);
      border: 1px solid rgba(16, 185, 129, 0.4);
      color: $accent-emerald;
    }

    &.badge-ongoing {
      background: rgba(245, 158, 11, 0.15);
      border: 1px solid rgba(245, 158, 11, 0.4);
      color: $accent-amber;
    }
  }

  .goal-desc {
    font-size: 0.84rem;
    color: $text-secondary;
    line-height: 1.45;
  }

  .mini-progress {
    display: flex;
    flex-direction: column;
    gap: 0.4rem;

    .mini-header {
      @include flex-between;
      font-size: 0.75rem;

      .mini-title {
        color: $text-muted;
        font-weight: 600;
      }

      .mini-count {
        color: #fff;
        font-family: $font-mono;
        font-size: 0.72rem;
      }
    }

    .mini-track {
      height: 5px;
      background: rgba(255, 255, 255, 0.06);
      border-radius: $radius-full;
      overflow: hidden;
    }

    .mini-fill {
      height: 100%;
      background: linear-gradient(90deg, $accent-purple 0%, $accent-emerald 100%);
      transition: width 0.3s ease;
    }
  }

  .levels-checklist {
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
    margin-top: auto;
    padding-top: 0.75rem;
    border-top: 1px solid rgba(255, 255, 255, 0.06);

    .level-item {
      display: flex;
      align-items: flex-start;
      gap: 0.75rem;
      padding: 0.65rem 0.75rem;
      border-radius: $radius-md;
      background: rgba(255, 255, 255, 0.02);
      border: 1px solid rgba(255, 255, 255, 0.05);
      text-align: left;
      transition: all $transition-fast;

      &:hover {
        background: rgba(255, 255, 255, 0.06);
        border-color: rgba(255, 255, 255, 0.12);
      }

      &.level-done {
        background: rgba(16, 185, 129, 0.06);
        border-color: rgba(16, 185, 129, 0.25);

        .level-name {
          color: #fff;
        }

        .check-icon {
          color: $accent-emerald;
          display: flex;
        }
      }

      .checkbox-indicator {
        margin-top: 0.15rem;
        flex-shrink: 0;

        .circle-icon {
          color: $text-muted;
          display: flex;
        }
      }

      .level-info {
        display: flex;
        flex-direction: column;
        gap: 0.15rem;

        .level-name {
          font-size: 0.85rem;
          font-weight: 600;
          color: $text-secondary;
          line-height: 1.3;
        }

        .level-notes {
          font-size: 0.72rem;
          color: $text-muted;
        }
      }
    }

    .no-levels-notice {
      display: flex;
      align-items: center;
      gap: 0.5rem;
      font-size: 0.75rem;
      color: $text-dim;
      padding: 0.5rem 0;
    }
  }

  .goals-footer {
    display: flex;
    justify-content: center;
    margin-top: 2.5rem;

    .reset-goals-btn {
      font-size: 0.78rem;
      color: $text-dim;
      padding: 0.5rem 1rem;
      border-radius: $radius-full;
      background: rgba(255, 255, 255, 0.02);
      border: 1px solid rgba(255, 255, 255, 0.05);
      transition: all $transition-fast;

      &:hover {
        color: $accent-red;
        border-color: rgba(255, 51, 102, 0.3);
      }
    }
  }
</style>
