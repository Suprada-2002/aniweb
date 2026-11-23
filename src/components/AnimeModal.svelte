<script>
  import { onMount } from 'svelte';
  import { X, Star, ExternalLink, Calendar, Film, Bookmark, Save, Check, Tag } from 'lucide-svelte';

  let { anime = null, onClose = () => {}, onTagClick = () => {} } = $props();

  let userNotes = $state('');
  let userRating = $state(0);
  let watchedEpisodes = $state('');
  let isSavedToast = $state(false);

  // Load saved journal entry from localStorage
  $effect(() => {
    if (anime) {
      try {
        const key = `aniweb_journal_${anime.id}`;
        const saved = localStorage.getItem(key);
        if (saved) {
          const parsed = JSON.parse(saved);
          userNotes = parsed.notes || '';
          userRating = parsed.userRating || 0;
          watchedEpisodes = parsed.watchedEpisodes || '';
        } else {
          userNotes = '';
          userRating = 0;
          watchedEpisodes = '';
        }
      } catch (e) {
        console.error('Error loading journal entry', e);
      }
    }
  });

  function saveJournalEntry() {
    if (!anime) return;
    try {
      const key = `aniweb_journal_${anime.id}`;
      const payload = {
        notes: userNotes,
        userRating,
        watchedEpisodes,
        lastUpdated: new Date().toISOString()
      };
      localStorage.setItem(key, JSON.stringify(payload));
      isSavedToast = true;
      setTimeout(() => {
        isSavedToast = false;
      }, 2500);
    } catch (e) {
      console.error('Error saving journal entry', e);
    }
  }

  function handleKeydown(e) {
    if (e.key === 'Escape') {
      onClose();
    }
  }

  let isCompleted = $derived(anime ? anime.status.toLowerCase().includes('completed') : false);
  let isOngoing = $derived(anime ? anime.status.toLowerCase().includes('ongoing') : false);
  let statusClass = $derived(
    isCompleted ? 'status-completed' : isOngoing ? 'status-ongoing' : 'status-not-watched'
  );
</script>

<svelte:window onkeydown={handleKeydown} />

{#if anime}
  <div 
    class="modal-backdrop" 
    onclick={(e) => e.target === e.currentTarget && onClose()}
    onkeydown={(e) => e.key === 'Escape' && onClose()}
    tabindex="-1"
    role="dialog"
    aria-modal="true"
  >
    <div class="modal-card">
      <button class="close-btn" onclick={onClose} aria-label="Close modal">
        <X size={20} />
      </button>

      <div class="modal-grid">
        <!-- Poster Left -->
        <div class="modal-poster-col">
          <div class="poster-wrapper">
            {#if anime.url}
              <img src={anime.url.trim()} alt={anime.name} />
            {:else}
              <div class="modal-fallback">
                <Film size={48} />
                <span>{anime.name}</span>
              </div>
            {/if}
            <div class="status-overlay">
              <span class="status-badge {statusClass}">
                {anime.status}
              </span>
            </div>
          </div>

          <div class="external-links">
            <a 
              href="https://anilist.co/search/anime?search={encodeURIComponent(anime.name)}" 
              target="_blank" 
              rel="noopener noreferrer"
              class="ext-btn anilist"
            >
              <ExternalLink size={14} />
              <span>AniList Page</span>
            </a>
          </div>
        </div>

        <!-- Info Right -->
        <div class="modal-info-col">
          <div class="modal-header">
            <div class="header-tags">
              <span class="id-tag">ENTRY #{anime.id}</span>
              <span class="type-tag">{anime.type || 'TV Series'}</span>
              {#if anime.year}
                <span class="year-tag"><Calendar size={13} /> {anime.year}</span>
              {/if}
            </div>

            <h2 class="anime-full-title">{anime.name}</h2>

            <div class="rating-row">
              <div class="global-score">
                <span class="star-fill"><Star size={16} fill="#fbbf24" /></span>
                <span class="score-num">{anime.score || '9.0'}</span>
                <span class="score-sub">/ 10</span>
              </div>
            </div>
          </div>

          <!-- Tags in Modal -->
          <div class="tags-section">
            <span class="section-label"><Tag size={13} /> Genres & Tags:</span>
            <div class="tags-wrap">
              {#if anime.genre && anime.genre.length > 0}
                {#each anime.genre as tag}
                  <button 
                    class="genre-chip" 
                    onclick={() => {
                      onTagClick(tag);
                      onClose();
                    }}
                  >
                    #{tag}
                  </button>
                {/each}
              {:else}
                <span class="no-tags">No tags</span>
              {/if}
            </div>
          </div>

          <!-- Synopsis / Description -->
          <div class="synopsis-section">
            <h4 class="section-title">Synopsis</h4>
            <p class="synopsis-text">
              {anime.description || 'No synopsis added yet for this title in the static records.'}
            </p>
          </div>

          <!-- Personal Journal Notes Section -->
          <div class="journal-entry-box">
            <div class="journal-box-header">
              <div class="box-title">
                <span class="bookmark-icon"><Bookmark size={15} /></span>
                <span>My Journal Log & Reflections</span>
              </div>
              {#if isSavedToast}
                <span class="toast-saved">
                  <Check size={14} /> Saved!
                </span>
              {/if}
            </div>

            <div class="journal-inputs">
              <textarea 
                bind:value={userNotes} 
                placeholder="Write your personal thoughts, favorite characters, memorable episodes, or journal review here..."
                rows="3"
              ></textarea>

              <div class="journal-actions-row">
                <input 
                  type="text" 
                  placeholder="Episodes/Season e.g. Ep 12/24" 
                  bind:value={watchedEpisodes}
                  class="episodes-input"
                />

                <button class="save-entry-btn" onclick={saveJournalEntry}>
                  <Save size={14} />
                  <span>Save Journal</span>
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
{/if}

<style lang="scss">
  @use '../styles/variables' as *;
  @use '../styles/mixins' as *;

  .modal-backdrop {
    position: fixed;
    inset: 0;
    z-index: 1000;
    background: rgba(4, 5, 8, 0.85);
    backdrop-filter: blur(12px);
    -webkit-backdrop-filter: blur(12px);
    @include flex-center;
    padding: 1.5rem;
    overflow-y: auto;
    animation: fadeIn 0.2s ease-out;

    @include mobile {
      padding: 0.75rem;
      align-items: flex-start;
    }
  }

  @keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  .modal-card {
    position: relative;
    width: 100%;
    max-width: 820px;
    background: #0f1017;
    border: 1px solid rgba(255, 255, 255, 0.12);
    border-radius: $radius-xl;
    box-shadow: 0 25px 60px rgba(0, 0, 0, 0.9), 0 0 35px rgba(255, 51, 102, 0.15);
    overflow: hidden;
    animation: slideUp 0.25s cubic-bezier(0.16, 1, 0.3, 1);

    @include mobile {
      margin: 1rem 0;
    }
  }

  @keyframes slideUp {
    from {
      opacity: 0;
      transform: translateY(20px) scale(0.97);
    }
    to {
      opacity: 1;
      transform: translateY(0) scale(1);
    }
  }

  .close-btn {
    position: absolute;
    top: 1.15rem;
    right: 1.15rem;
    z-index: 10;
    width: 36px;
    height: 36px;
    border-radius: 50%;
    background: rgba(255, 255, 255, 0.08);
    color: $text-secondary;
    @include flex-center;
    transition: all $transition-fast;

    &:hover {
      background: rgba(255, 51, 102, 0.25);
      color: #fff;
      transform: rotate(90deg);
    }
  }

  .modal-grid {
    display: grid;
    grid-template-columns: 280px 1fr;
    gap: 1.75rem;
    padding: 2rem;

    @include mobile {
      grid-template-columns: 1fr;
      padding: 1.25rem;
      gap: 1.25rem;
    }
  }

  .modal-poster-col {
    display: flex;
    flex-direction: column;
    gap: 1rem;

    .poster-wrapper {
      position: relative;
      width: 100%;
      aspect-ratio: 1 / 1.45;
      border-radius: $radius-lg;
      overflow: hidden;
      background: #08090d;
      border: 1px solid rgba(255, 255, 255, 0.08);

      @include mobile {
        max-width: 220px;
        margin: 0 auto;
      }

      img {
        width: 100%;
        height: 100%;
        object-fit: cover;
      }

      .modal-fallback {
        width: 100%;
        height: 100%;
        @include flex-center;
        flex-direction: column;
        gap: 0.5rem;
        color: $text-muted;
        text-align: center;
        padding: 1rem;
      }

      .status-overlay {
        position: absolute;
        bottom: 0.75rem;
        left: 0.75rem;
      }
    }

    .external-links {
      display: flex;
      flex-direction: column;
      gap: 0.5rem;

      .ext-btn {
        display: flex;
        align-items: center;
        justify-content: center;
        gap: 0.5rem;
        padding: 0.65rem;
        border-radius: $radius-md;
        font-size: 0.8rem;
        font-weight: 600;
        background: rgba(255, 255, 255, 0.04);
        border: 1px solid rgba(255, 255, 255, 0.08);
        color: $text-secondary;
        transition: all $transition-fast;

        &:hover {
          background: rgba(255, 255, 255, 0.1);
          color: #fff;
          border-color: rgba(255, 255, 255, 0.2);
        }
      }
    }
  }

  .modal-info-col {
    display: flex;
    flex-direction: column;
    gap: 1.25rem;
  }

  .header-tags {
    display: flex;
    align-items: center;
    gap: 0.5rem;
    font-size: 0.75rem;
    font-family: $font-mono;
    margin-bottom: 0.5rem;

    .id-tag {
      background: rgba(255, 51, 102, 0.15);
      color: $accent-red;
      padding: 0.15rem 0.5rem;
      border-radius: $radius-sm;
      font-weight: 700;
    }

    .type-tag {
      background: rgba(139, 92, 246, 0.15);
      color: #c084fc;
      padding: 0.15rem 0.5rem;
      border-radius: $radius-sm;
    }

    .year-tag {
      display: inline-flex;
      align-items: center;
      gap: 0.25rem;
      color: $text-secondary;
    }
  }

  .anime-full-title {
    font-size: 1.75rem;
    font-weight: 800;
    color: #fff;
    line-height: 1.2;

    @include mobile {
      font-size: 1.35rem;
    }
  }

  .rating-row {
    margin-top: 0.4rem;

    .global-score {
      display: inline-flex;
      align-items: center;
      gap: 0.4rem;
      background: rgba(255, 255, 255, 0.04);
      padding: 0.25rem 0.65rem;
      border-radius: $radius-full;
      border: 1px solid rgba(255, 255, 255, 0.08);

      .star-fill {
        color: #fbbf24;
        display: flex;
      }

      .score-num {
        font-size: 1.1rem;
        font-weight: 800;
        color: #fff;
        font-family: $font-mono;
      }

      .score-sub {
        font-size: 0.75rem;
        color: $text-muted;
      }
    }
  }

  .tags-section {
    display: flex;
    flex-direction: column;
    gap: 0.4rem;

    .section-label {
      display: inline-flex;
      align-items: center;
      gap: 0.35rem;
      font-size: 0.75rem;
      font-weight: 700;
      color: $text-muted;
      text-transform: uppercase;
      letter-spacing: 0.05em;
    }

    .tags-wrap {
      display: flex;
      flex-wrap: wrap;
      gap: 0.4rem;

      .genre-chip {
        padding: 0.25rem 0.65rem;
        border-radius: $radius-sm;
        font-size: 0.78rem;
        font-weight: 600;
        background: rgba(255, 255, 255, 0.05);
        border: 1px solid rgba(255, 255, 255, 0.1);
        color: $text-secondary;
        transition: all $transition-fast;

        &:hover {
          background: rgba(255, 51, 102, 0.18);
          color: #fff;
          border-color: rgba(255, 51, 102, 0.45);
        }
      }
    }
  }

  .synopsis-section {
    .section-title {
      font-size: 0.82rem;
      font-weight: 700;
      color: $text-muted;
      text-transform: uppercase;
      letter-spacing: 0.05em;
      margin-bottom: 0.4rem;
    }

    .synopsis-text {
      font-size: 0.88rem;
      line-height: 1.6;
      color: $text-secondary;
    }
  }

  .journal-entry-box {
    background: rgba(255, 255, 255, 0.02);
    border: 1px solid rgba(255, 255, 255, 0.08);
    border-radius: $radius-lg;
    padding: 1rem;
    display: flex;
    flex-direction: column;
    gap: 0.75rem;

    .journal-box-header {
      @include flex-between;

      .box-title {
        display: flex;
        align-items: center;
        gap: 0.45rem;
        font-size: 0.82rem;
        font-weight: 700;
        color: #fff;

        .bookmark-icon {
          color: $accent-red;
          display: flex;
        }
      }

      .toast-saved {
        display: inline-flex;
        align-items: center;
        gap: 0.3rem;
        font-size: 0.75rem;
        color: $accent-emerald;
        font-weight: 600;
        animation: fadeIn 0.2s ease-in;
      }
    }

    .journal-inputs {
      display: flex;
      flex-direction: column;
      gap: 0.65rem;

      textarea {
        width: 100%;
        background: rgba(0, 0, 0, 0.4);
        border: 1px solid rgba(255, 255, 255, 0.08);
        border-radius: $radius-md;
        padding: 0.75rem;
        color: #fff;
        font-size: 0.85rem;
        resize: vertical;
        line-height: 1.5;
        @include custom-scrollbar;

        &::placeholder {
          color: $text-muted;
        }

        &:focus {
          border-color: rgba(255, 51, 102, 0.5);
        }
      }
    }

    .journal-actions-row {
      display: flex;
      gap: 0.65rem;

      @include mobile {
        flex-direction: column;
      }

      .episodes-input {
        flex: 1;
        background: rgba(0, 0, 0, 0.4);
        border: 1px solid rgba(255, 255, 255, 0.08);
        border-radius: $radius-md;
        padding: 0.5rem 0.75rem;
        font-size: 0.82rem;
        color: #fff;

        &::placeholder {
          color: $text-muted;
        }

        &:focus {
          border-color: rgba(255, 51, 102, 0.5);
        }
      }

      .save-entry-btn {
        display: inline-flex;
        align-items: center;
        gap: 0.45rem;
        padding: 0.5rem 1rem;
        background: linear-gradient(135deg, $accent-red 0%, #b31238 100%);
        color: #fff;
        font-size: 0.82rem;
        font-weight: 700;
        border-radius: $radius-md;
        box-shadow: 0 0 15px rgba(255, 51, 102, 0.3);
        transition: all $transition-fast;
        white-space: nowrap;

        &:hover {
          box-shadow: 0 0 20px rgba(255, 51, 102, 0.5);
          transform: translateY(-1px);
        }
      }
    }
  }

  .status-badge {
    display: inline-flex;
    align-items: center;
    padding: 0.25rem 0.65rem;
    border-radius: $radius-full;
    font-size: 0.72rem;
    font-weight: 700;
    backdrop-filter: blur(8px);

    &.status-completed {
      background: rgba(16, 185, 129, 0.25);
      border: 1px solid rgba(16, 185, 129, 0.6);
      color: #34d399;
    }

    &.status-ongoing {
      background: rgba(245, 158, 11, 0.25);
      border: 1px solid rgba(245, 158, 11, 0.6);
      color: #fbbf24;
    }

    &.status-not-watched {
      background: rgba(148, 163, 184, 0.25);
      border: 1px solid rgba(148, 163, 184, 0.5);
      color: #cbd5e1;
    }
  }
</style>
