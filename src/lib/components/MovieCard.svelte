<script lang="ts">
  import type { Movie } from '$lib/types';

  // Props con Svelte 5: sistema de tipos explícito y callbacks en lugar de eventos
  let { 
    movie,
    showActions = true,
    ondelete,
    onedit,
    ontogglefavorite,
    onrate
  }: {
    movie: Movie;
    showActions?: boolean;
    ondelete?: (id: string) => void;
    onedit?: (movie: Movie) => void;
    ontogglefavorite?: (id: string) => void;
    onrate?: (id: string, rating: number) => void;
  } = $props();

  // Handlers: ejecutan callbacks del padre directamente
  function handleDelete() {
    ondelete?.(movie.id);
  }

  function handleEdit() {
    onedit?.(movie);
  }

  function handleToggleFavorite() {
    ontogglefavorite?.(movie.id);
  }

  function handleRate(rating: number) {
    onrate?.(movie.id, rating);
  }
</script>

<!-- Componente reutilizable: tarjeta para mostrar información de una película -->
<article class="flex flex-col overflow-hidden rounded-lg border border-slate-200 bg-white shadow-sm">
  {#if movie.posterUrl}
    <div class="flex h-48 items-center justify-center bg-slate-100">
      <img
        alt={`Póster de ${movie.title}`}
        class="max-h-full max-w-full object-contain"
        src={movie.posterUrl}
        loading="lazy"
      />
    </div>
  {/if}

  <div class="flex flex-1 flex-col gap-3 p-4">
    <header class="flex items-start justify-between">
      <div>
        <h3 class="text-lg font-semibold text-slate-900">{movie.title}</h3>
        <p class="text-sm text-slate-600">Dirigida por {movie.director}</p>
      </div>
      {#if showActions}
        <button
          type="button"
          class="text-2xl transition hover:scale-110"
          title={movie.isFavorite ? 'Quitar de favoritos' : 'Añadir a favoritos'}
          onclick={handleToggleFavorite}
        >
          {movie.isFavorite ? '❤️' : '🤍'}
        </button>
      {/if}
    </header>

    <!-- Valoración -->
    {#if showActions}
      <div class="mt-2 flex items-center gap-1">
        {#each [1, 2, 3, 4, 5] as star}
          <button
            type="button"
            class="rounded text-xl focus:outline-none focus:ring-2 focus:ring-indigo-500"
            onclick={() => handleRate(star)}
            title={`Puntuar con ${star} estrellas`}
          >
            {#if (movie.rating ?? 0) >= star}
              <span class="text-yellow-400">★</span>
            {:else}
              <span class="text-gray-300">☆</span>
            {/if}
          </button>
        {/each}
      </div>
    {/if}

    <div class="text-sm text-slate-500">
      {#if movie.year}
        <span>Año: {movie.year}</span>
      {/if}
    </div>

    {#if showActions}
      <div class="mt-3 flex flex-col gap-2 sm:flex-row">
        <button
          type="button"
          class="w-full rounded border border-slate-300 px-3 py-2 text-slate-700 transition hover:bg-slate-50"
          onclick={handleEdit}
        >
          Editar
        </button>
        <button
          type="button"
          class="w-full rounded border border-red-500 px-3 py-2 text-red-600 transition hover:bg-red-50"
          onclick={handleDelete}
        >
          Eliminar
        </button>
      </div>
    {/if}
  </div>
</article>