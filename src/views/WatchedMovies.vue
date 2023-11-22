<template>
  <div>
    <Watched :watched="watched" @show-movie-details="displayMovieDetails" />
  </div>
</template>

<script>
  import MovieCard from "../components/MovieCard.vue";
  import Watched from "../components/Watched.vue";
  import Reviews from '../components/Reviews.vue';
  import axios from "axios";


  export default {
    components: {
      MovieCard,
      Watched,
      Reviews,
    },
    data() {
      return {
        movies: [],
        selectedMovie: null,
        watched: [],
        lovethis: [],
        searchQuery: '',
      };
    },
    mounted() {
      this.fetchMovies();
      const watchedMovies = localStorage.getItem('watchedMovies');
      if (watchedMovies) {
        this.watched = JSON.parse(watchedMovies);
      }
    },
    computed: {
      filteredMovies() {
        if (!this.searchQuery) {
          return this.movies;
        }

        const lowerCaseQuery = this.searchQuery.toLowerCase();
        return this.movies.filter((movie) =>
          movie.title.toLowerCase().includes(lowerCaseQuery)
        );
      },
    },
    methods: {
      async fetchMovies() {
        try {
          const apiKey = "fc6c2ae3879fb613913161f02e79c1ed";
          const response = await axios.get(
            `https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}`
          );
          this.movies = response.data.results;
        } catch (error) {
          console.error(error);
        }
      },
      displayMovieDetails(movie) {
        this.selectedMovie = movie;
        this.fetchTrailerKey(movie.id);
      },
      toggleMarkAsWatched() {
        this.selectedMovie.watched = !this.selectedMovie.watched;
        if (this.selectedMovie.watched) {
          this.watched.push(this.selectedMovie);
          localStorage.setItem('watchedMovies', JSON.stringify(this.watched));
        } else {
          const index = this.watched.findIndex((movie) => movie.id === this.selectedMovie.id);
          if (index !== -1) {
            this.watched.splice(index, 1);
            localStorage.setItem('watchedMovies', JSON.stringify(this.watched));
          }

        }


        console.log("Watched");
      },
      toggleLoveThis() {
        this.selectedMovie.lovethis = !this.selectedMovie.lovethis;
        if (this.selectedMovie.lovethis) {
          this.lovethis.push(this.selectedMovie);
          this.selectedMovie.backgroundColor = '#fd79a8';
        } else {
          const index = this.lovethis.findIndex((movie) => movie.id === this.selectedMovie.id);
          if (index !== -1) {
            this.lovethis.splice(index, 1);
          }
          this.selectedMovie.backgroundColor = '';
        }
        console.log("Love This");
      },
      closeModal() {
        this.selectedMovie = null;
      },
      async fetchTrailerKey(movieId) {
        try {
          const apiKey = "AIzaSyDqYtBTikXRl1c_sj7cKHSEDXuhIW3movk";
          const response = await axios.get(
            `https://www.googleapis.com/youtube/v3/search?part=snippet&q=${encodeURIComponent(
            this.selectedMovie.title + " trailer"
          )}&maxResults=1&type=video&key=${apiKey}`
          );
          if (response.data.items.length > 0) {
            this.selectedMovie.trailerKey = response.data.items[0].id.videoId;
          } else {
            this.selectedMovie.trailerKey = null;
          }
        } catch (error) {
          console.error(error);
          this.selectedMovie.trailerKey = null;
        }
      },
      getVideoEmbedUrl(videoKey) {
        return `https://www.youtube.com/embed/${videoKey}`;
      },
    },
  };
</script>

<style scoped>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
</style>