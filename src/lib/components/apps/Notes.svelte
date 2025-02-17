<script lang="ts">
  import { slide, fade } from 'svelte/transition';
  import { quintOut } from 'svelte/easing';
  import { flip } from 'svelte/animate';
  import { onMount } from 'svelte';

  let searchInput: HTMLInputElement;

  interface Note {
    id: string;
    title: string;
    content: string;
    lastModified: Date;
    emoji: string;
  }

  let notes: Note[] = [];
  let selectedNote: Note | null = null;
  let isEditing = false;
  let newNoteTitle = '';
  let newNoteContent = '';
  let searchQuery = '';
  let showSearch = false;

  onMount(() => {
    if (showSearch && searchInput) {
      searchInput.focus();
    }
  });

  function toggleSearch() {
    showSearch = !showSearch;
    if (showSearch) {
      setTimeout(() => {
        searchInput?.focus();
      }, 0);
    } else {
      searchQuery = '';
    }
  }

  function formatDate(date: Date): string {
    const now = new Date();
    const diff = now.getTime() - new Date(date).getTime();
    const days = Math.floor(diff / (1000 * 60 * 60 * 24));
    const hours = Math.floor(diff / (1000 * 60 * 60));
    const minutes = Math.floor(diff / (1000 * 60));

    if (days > 0) return `${days}d ago`;
    if (hours > 0) return `${hours}h ago`;
    if (minutes > 0) return `${minutes}m ago`;
    return 'Just now';
  }

  function getRandomEmoji() {
    const emojis = ['📝', '📓', '📔', '📕', '📗', '📘', '📙'];
    return emojis[Math.floor(Math.random() * emojis.length)];
  }

  function createNote() {
    const newNote = {
      id: crypto.randomUUID(),
      title: 'Untitled Note',
      content: '',
      lastModified: new Date(),
      emoji: getRandomEmoji()
    };
    notes = [newNote, ...notes];
    selectedNote = newNote;
    isEditing = true;
    newNoteTitle = newNote.title;
    newNoteContent = newNote.content;
  }

  function selectNote(note: Note) {
    selectedNote = note;
    isEditing = false;
    newNoteTitle = note.title;
    newNoteContent = note.content;
  }

  function saveNote() {
    if (!selectedNote) return;

    const updatedNote = {
      ...selectedNote,
      title: newNoteTitle || 'Untitled Note',
      content: newNoteContent,
      lastModified: new Date()
    };
    notes = notes.map(note =>
      note.id === selectedNote.id ? updatedNote : note
    );
    selectedNote = updatedNote;
    isEditing = false;
  }

  function deleteNote(noteId: string) {
    notes = notes.filter(note => note.id !== noteId);
    if (selectedNote?.id === noteId) {
      selectedNote = null;
    }
  }

  $: filteredNotes = searchQuery
    ? notes.filter(note =>
        note.title.toLowerCase().includes(searchQuery.toLowerCase()) ||
        note.content.toLowerCase().includes(searchQuery.toLowerCase())
      )
    : notes;

  $: wordCount = newNoteContent.trim().split(/\s+/).length;
  $: characterCount = newNoteContent.length;
</script>

<div class="h-full bg-gradient-to-br from-gray-900 via-blue-900/20 to-gray-800 flex">
  <div class="w-[280px] border-r border-white/5 flex flex-col bg-black/20 backdrop-blur-lg">
    <div class="px-4 h-12 flex justify-between items-center border-b border-white/5">
      {#if showSearch}
        <input
          type="text"
          bind:value={searchQuery}
          bind:this={searchInput}
          placeholder="Search notes..."
          class="w-full bg-transparent text-white/90 text-sm border-none outline-none placeholder-white/30"
          transition:slide
        />
        <button
          class="ml-2 text-white/50 hover:text-white/90 transition-colors"
          on:click={toggleSearch}
        >
          ✕
        </button>
      {:else}
        <div class="flex items-center gap-3">
          <h2 class="text-base font-medium text-white/90">Notes</h2>
          <button
            class="text-white/50 hover:text-white/90 transition-colors text-sm"
            on:click={toggleSearch}
          >
            🔍
          </button>
        </div>
        <button
          class="w-7 h-7 rounded-full bg-blue-500/90 text-white text-lg flex items-center justify-center hover:bg-blue-500 hover:scale-105 active:scale-95 transition-all shadow-lg shadow-blue-500/20 hover:shadow-blue-500/40"
          on:click={createNote}
          aria-label="Create new note"
        >
          +
        </button>
      {/if}
    </div>

    <div class="flex-1 overflow-y-auto py-2 scrollbar-thin scrollbar-thumb-white/10 scrollbar-track-transparent" role="list">
      {#each filteredNotes as note (note.id)}
        <div
          class="group px-2"
          role="listitem"
          transition:slide={{ duration: 200 }}
          animate:flip={{ duration: 200 }}
        >
          <div
            class={`flex items-center rounded-xl mb-1 hover:bg-white/5 hover:translate-x-1 transition-all cursor-pointer ${selectedNote?.id === note.id ? 'bg-white/10' : ''}`}
            on:click={() => selectNote(note)}
            on:keydown={e => {
              if (e.key === 'Enter' || e.key === ' ') {
                selectNote(note);
              }
            }}
            role="button"
            tabindex="0"
            aria-label={`Note: ${note.title}`}
          >
            <div class="flex-1 py-2.5 px-3 min-w-0">
              <div class="flex items-center gap-2">
                <span class="text-white/40 text-lg" transition:fade>{note.emoji}</span>
                <h3 class="text-sm font-medium text-white/90 truncate">{note.title}</h3>
              </div>
              <p class="text-xs text-white/50 truncate mt-0.5">{note.content.slice(0, 50)}...</p>
              <span class="text-[10px] text-white/30 mt-1 block">{formatDate(note.lastModified)}</span>
            </div>
            <button
              type="button"
              class="px-2 py-4 text-red-400/70 opacity-0 group-hover:opacity-100 hover:text-red-400 hover:bg-red-400/10 rounded-r-xl transition-all flex items-center"
              on:click|stopPropagation={() => deleteNote(note.id)}
              aria-label={`Delete note: ${note.title}`}
            >
              🗑️
            </button>
          </div>
        </div>
      {/each}
    </div>
  </div>

  <div class="flex-1 flex flex-col bg-black/10 backdrop-blur-lg">
    {#if selectedNote}
      {#if isEditing}
        <div class="h-12 px-4 border-b border-white/5 flex items-center justify-between gap-3">
          <div class="flex items-center gap-2 flex-1">
            <span class="text-white/40 text-lg">{selectedNote.emoji}</span>
            <input
              type="text"
              bind:value={newNoteTitle}
              placeholder="Note Title"
              class="flex-1 bg-transparent text-white/90 text-base font-medium border-none outline-none placeholder-white/30"
              aria-label="Note title"
            />
          </div>
          <div class="flex items-center gap-3">
            <span class="text-white/30 text-xs">
              {wordCount} words, {characterCount} chars
            </span>
            <button
              type="button"
              class="px-4 h-8 bg-blue-500/90 text-white/90 text-sm rounded-lg hover:bg-blue-500 hover:scale-105 active:scale-95 transition-all shadow-lg shadow-blue-500/20"
              on:click={saveNote}
            >
              Save
            </button>
          </div>
        </div>
        <textarea
          bind:value={newNoteContent}
          placeholder="Start writing..."
          class="flex-1 p-4 bg-transparent text-white/80 text-sm leading-relaxed resize-none border-none outline-none placeholder-white/30 scrollbar-thin scrollbar-thumb-white/10 scrollbar-track-transparent"
          aria-label="Note content"
        ></textarea>
      {:else}
        <div class="h-12 px-4 border-b border-white/5 flex items-center justify-between">
          <div class="flex items-center gap-2">
            <span class="text-white/40 text-lg" transition:fade>{selectedNote.emoji}</span>
            <h2 class="text-base font-medium text-white/90">{selectedNote.title}</h2>
          </div>
          <div class="flex items-center gap-3">
            <span class="text-white/30 text-xs">
              {selectedNote.content.trim().split(/\s+/).length} words
            </span>
            <button
              type="button"
              class="px-4 h-8 bg-blue-500/90 text-white/90 text-sm rounded-lg hover:bg-blue-500 hover:scale-105 active:scale-95 transition-all shadow-lg shadow-blue-500/20"
              on:click={() => isEditing = true}
            >
              Edit
            </button>
          </div>
        </div>
        <div class="flex-1 p-4 overflow-y-auto scrollbar-thin scrollbar-thumb-white/10 scrollbar-track-transparent">
          {#each selectedNote.content.split('\n') as line}
            <p class="text-sm text-white/70 mb-3 leading-relaxed">{line}</p>
          {/each}
        </div>
      {/if}
    {:else}
      <div class="flex-1 flex flex-col items-center justify-center text-white/30 gap-3" in:fade={{ duration: 200 }}>
        <span class="text-6xl animate-bounce">📝</span>
        <p class="text-sm">Select a note or create a new one</p>
      </div>
    {/if}
  </div>
</div>

<style>
  /* Tailwind scrollbar classes */
  .scrollbar-thin {
    scrollbar-width: thin;
  }
  .scrollbar-thumb-white\/10 {
    scrollbar-color: rgba(255, 255, 255, 0.1) transparent;
  }
  .scrollbar-track-transparent {
    scrollbar-track-color: transparent;
  }

  /* Webkit scrollbar styles */
  .scrollbar-thin::-webkit-scrollbar {
    width: 6px;
  }
  .scrollbar-thumb-white\/10::-webkit-scrollbar-thumb {
    background-color: rgba(255, 255, 255, 0.1);
    border-radius: 3px;
  }
  .scrollbar-track-transparent::-webkit-scrollbar-track {
    background: transparent;
  }

  /* Animation keyframes */
  @keyframes bounce {
    0%, 100% {
      transform: translateY(0);
    }
    50% {
      transform: translateY(-10px);
    }
  }
</style>
