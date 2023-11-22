<template>
  <div class="movie-card" @click="showDetails">
    <img :src="getFullPosterUrl(movie.poster_path)" :alt="movie.title" class="movie-poster" />
    <h4 class="movie-title">{{ movie.title }}</h4>
    <p class="movie-release">{{ movie.release_date }}</p>
  </div>
</template>

<script>
export default {
  props: {
    movie: {
      type: Object,
      required: true,
    },
  },
  methods: {
    getFullPosterUrl(posterPath) {
      const baseImageUrl = "https://image.tmdb.org/t/p/w500";
      return `${baseImageUrl}${posterPath}`;
    },
    showDetails() {
      this.$emit('show-movie-details', this.movie);
    },
  },
};
</script>

<style scoped>
.movie-card {
  text-align: center;
  width: 250px;
  margin: 20px;
  background-color:#2c3e50;
  /* padding: 15px; */
  border-radius: 15px;
  cursor: pointer;
  box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
}
.movie-card:hover {
  outline: 3px solid #00D735;
}

.movie-poster {
  width: 100%;
  height: auto;
  border-top-right-radius: 15px;
  border-top-left-radius: 15px;
}
.movie-title {
  margin: 10px auto;
  color: white;
  width: 13em;
  /* padding: 15px; */
}

.movie-release {
  color: white;
}
</style>
