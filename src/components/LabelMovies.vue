<template>
  <div class="label-parent">
    <div class="label-movies" :class="{ labelmovies: isWatched === true }">
      <span @click="$emit('toggleWatched')">😎 {{ watched.length }}/{{ movies.length }} Watched Movies</span>
    </div>
    <div class="label-movies">
      <span>❤️ {{ lovethis.length }}/{{ movies.length }} Loved Movies</span>
    </div>
    <div class="label-movies" :class="{ labelmovies: isReviewed === true }">
      <span @click="$emit('toggleReviewed')">✍️ {{ reviews.length }}/{{ movies.length }} Reviewed Movies</span>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    watched: {
      type: Array,
      required: true
    },
    movies: {
      type: Array,
      required: true
    },
    lovethis: {
      type: Array,
      required: true
    },
    reviews: {
      type: Array,
      required: true
    },
    isWatched: {
      type: Boolean,
      required: true
    },
    isReviewed: {
      type: Boolean,
      required: true
    }
  }
};
</script>

<style scoped>
/* Add the styles from the original code for label-movies and labelmovies classes */
.label-parent {
    display: flex;
    justify-content: center;
    align-items: center;
    flex-wrap: wrap;
  }

  .label-movies {
    display: flex;
    justify-content: center;
    align-items: center;
    margin-top: 30px;
    margin-right: 5px;
    margin-left: 5px;
    width: 17em;
    height: 2em;
    border-radius: 15px;
    background-color: #2c3e50;
    color: white;
    cursor: pointer;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .label-movies:hover {
    background-color: #525b64;
  }

  .labelmovies {
    background-color: #525b64;
  }
</style>
