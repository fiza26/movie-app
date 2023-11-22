<template>
  <div>
    <hr>
    <div class="search-bar">
      <input type="text" v-model="searchQuery" placeholder="Search movies..." />
    </div>

    <LabelMovies :watched="watched" :movies="movies" :lovethis="lovethis" :reviews="reviews" :isWatched="isWatched"
      :isReviewed="isReviewed" @toggleWatched="toggleWatched" @toggleReviewed="toggleReviewed" />

    <div class="movies-container" v-if="isWatched === false && isReviewed === false">
      <MovieCard v-for="movie in filteredMovies" :key="movie.id" :movie="movie"
        @show-movie-details="displayMovieDetails" />
    </div>

    <div v-if="isWatched === true && isReviewed === false">
      <Watched :watched="watched" />
    </div>

    <div v-if="isReviewed === true">
      <Reviews :reviews="reviews" :selectedMovie="selectedMovie" />
    </div>

    <div class="pagination" v-show="isWatched === false && isReviewed === false">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>


    <!-- Write Review Modal -->
    <div v-if="isToggled" class="modal-review">
      <p @click="isToggled = !isToggled" class="write-review">❌</p>

      <div class="modal-content-review">
        <h2>{{ selectedMovie.title }}</h2>
        <p v-if="selectedMovie.vote_average > 8">⭐⭐⭐⭐⭐</p>
        <p v-else-if="selectedMovie.vote_average > 7">⭐⭐⭐</p>
        <p v-else>⭐</p>
        <input type="textarea" v-model="review" placeholder="Write your review here..." v-if="!isMovieReviewed"><br>
        <button @click="submitReview" v-if="!isMovieReviewed">✍️ Submit</button>
        <p v-if="isMovieReviewed">You reviewed this movie already<br><br>Check on : <RouterLink to="/reviewed">
            <p>Reviewed Movies</p>
          </RouterLink>
        </p>
        <br>
        <br>
        <span v-show="notification === true">🎉 Your review is submitted</span>
        <!-- <Reviews :reviews="reviews" :selectedMovie="selectedMovie" /> -->

      </div>
    </div>

    <!-- Movie details modal -->
    <div v-if="selectedMovie" class="modal" :style="{ backgroundColor: selectedMovie.backgroundColor }">
      <div class="modal-content">
        <p @click="isToggled = !isToggled" class="write-review">✍️</p>
        <h2>{{ selectedMovie.title }}</h2>
        <p>Release Date: {{ selectedMovie.release_date }}</p>
        <p>Rating : {{ selectedMovie.vote_average }}</p>
        <p v-if="selectedMovie.vote_average > 8">⭐⭐⭐⭐⭐</p>
        <p v-else-if="selectedMovie.vote_average > 7">⭐⭐⭐</p>
        <p v-else>⭐</p>
        <div v-if="selectedMovie.trailerKey" class="video-container">
          <iframe :src="getVideoEmbedUrl(selectedMovie.trailerKey)" frameborder="0" allowfullscreen></iframe>
        </div>
        <p>Overview: {{ selectedMovie.overview }}</p>
        <button @click="toggleMarkAsWatched" :class="{watched: isWatchedIt }">😎 Watched It?</button>
        <button @click="toggleMarkAsWatched" v-show="isWatchedIt">😎 Yes Watched</button>

        <!-- <button @click="loveThis" :class="{ lovethis:selectedMovie.lovethis }">❤️ Love This?</button><button v-show="selectedMovie.lovethis">🥰</button> -->
        <button @click="toggleLoveThis" :class="{ lovethis: isLovedIt }">
          ❤️ Love This?
        </button>
        <button @click="toggleLoveThis" v-show="isLovedIt">
          ❤️ Loved
        </button>
        <button @click="closeModal">❌ Close</button>
        <br>
        <br>
        <!-- <span v-show="selectedMovie.watched">😎 You Have Watched This Movie!</span> -->
      </div>
    </div>
  </div>
</template>

<script>
  // import { watch } from 'vue';
  import {
    RouterView
  } from 'vue-router'
  import MovieCard from "../components/MovieCard.vue";
  import Watched from "../components/Watched.vue";
  import Reviews from '../components/Reviews.vue';
  import LabelMovies from '../components/LabelMovies.vue';
  import axios from "axios";


  export default {
    components: {
      MovieCard,
      Watched,
      Reviews,
      LabelMovies,
    },
    data() {
      return {
        movies: [],
        selectedMovie: null,
        watched: [],
        lovethis: [],
        isToggled: false,
        review: '',
        reviews: [],
        notification: false,
        isWatched: false,
        isReviewed: false,
        searchQuery: '',
        currentPage: 1,
        totalPages: 0,
        totalResults: 0
      };
    },
    mounted() {
      this.fetchMovies();
      const watchedMovies = localStorage.getItem('watchedMovies');
      if (watchedMovies) {
        this.watched = JSON.parse(watchedMovies);
      }
      const lovedMovies = localStorage.getItem('lovedMovies');
      if (lovedMovies) {
        this.lovethis = JSON.parse(lovedMovies);
      }
      const reviewedMovies = localStorage.getItem('reviewedMovies');
      if (reviewedMovies) {
        this.reviews = JSON.parse(reviewedMovies);
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
      isMovieReviewed() {
        return this.reviews.some(review => review.movie.id === this.selectedMovie.id);
      },
      isWatchedIt() {
        const isWatchedIt = this.watched.some(watch => watch.id === this.selectedMovie.id);
        console.log('isWatchedIt:', isWatchedIt);
        return isWatchedIt;
      },
      isLovedIt() {
        return this.lovethis.some(love => love.id === this.selectedMovie.id);
      }
    },
    methods: {
      async fetchMovies() {
        try {
          const apiKey = "fc6c2ae3879fb613913161f02e79c1ed";
          const response = await axios.get(
            `https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&page=${this.currentPage}`
          );
          this.movies = response.data.results;
          this.totalPages = response.data.total_pages;
          this.totalResults = response.data.total_results;
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
        if (this.selectedMovie.watched && !this.isWatchedIt) {
          this.watched.push(this.selectedMovie);
          localStorage.setItem('watchedMovies', JSON.stringify(this.watched));
        } else if (this.isWatchedIt) {
          const index = this.watched.findIndex((movie) => movie.id === this.selectedMovie.id);
          if (index !== -1) {
            this.watched.splice(index, 1);
            localStorage.setItem('watchedMovies', JSON.stringify(this.watched));
          }

        }


        console.log("Watched");
      },
      toggleLoveThis() {
        if (!this.isLovedIt) {
          this.lovethis.push(this.selectedMovie);
          this.selectedMovie.backgroundColor = '#fd79a8';
          localStorage.setItem('lovedMovies', JSON.stringify(this.lovethis));
        } else if (this.isLovedIt) {
          const index = this.lovethis.findIndex((movie) => movie.id === this.selectedMovie.id);
          if (index !== -1) {
            this.lovethis.splice(index, 1);
          }
          this.selectedMovie.backgroundColor = '';
          localStorage.setItem('lovedMovies', JSON.stringify(this.lovethis));
        }
        console.log("Love This");
      },
      submitReview() {
        if (this.review.trim() !== '') {
          const reviewData = {
            id: Date.now(),
            movie: this.selectedMovie,
            review: this.review
          };

          this.reviews.push(reviewData);
          this.review = '';
          console.log(reviewData);
          this.notification = true;
          localStorage.setItem('reviewedMovies', JSON.stringify(this.reviews));
        }
      },
      toggleWatched() {
        this.isWatched = !this.isWatched;
      },
      toggleReviewed() {
        this.isReviewed = !this.isReviewed;
      },

      closeModal() {
        this.selectedMovie = null;
        this.notification = false;
      },
      prevPage() {
        if (this.currentPage > 1) {
          this.currentPage--;
          this.fetchMovies();
        }
      },
      nextPage() {
        if (this.currentPage < this.totalPages) {
          this.currentPage++;
          this.fetchMovies(); // Add this line to fetch movies for the next page
        }
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

<style>
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-family: 'Poppins', sans-serif;
  }

  body {
    background-color: #dddd;
  }

  .search-bar {
    display: flex;
    align-items: center;
    justify-content: center;
    width: 100%;
    background-color: #2c3e50;
    height: 5em;
  }

  .search-bar input {
    width: 70%;
    height: 30px;
    border: none;
    padding: 7px;
    border-radius: 15px;
    background-color: #525b64;
  }

  .search-bar input:focus {
    background-color: white;
  }

  .write-review {
    display: flex;
    justify-content: center;
    align-items: center;
    position: absolute;
    top: 20px;
    right: 20px;
    border-radius: 50px;
    width: 40px;
    height: 40px;
    background-color: #2c3e50;
    cursor: pointer;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .write-review:hover {
    background-color: #525b64;
  }

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

  .movies-container {
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
  }

  .modal {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background-color 0.5s ease;
  }


  .modal-content {
    transform: scale(0);
    animation: moveUp 0.5s cubic-bezier(0.165, 0.840, 0.440, 1.000) forwards;
    background-color: white;
    padding: 20px;
    border-radius: 15px;
    max-width: 600px;
    margin-right: 7px;
    margin-left: 7px;
    text-align: center;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .modal-review {
    z-index: 1;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: #2c3e50;
    display: flex;
    align-items: center;
    justify-content: center;
    transform: scale(0);
    animation: moveUp 0.5s cubic-bezier(0.165, 0.840, 0.440, 1.000) forwards;
    transition: background-color 0.1s ease;
  }

  .modal-content-review {
    transform: scale(0);
    animation: moveUp 0.5s cubic-bezier(0.165, 0.840, 0.440, 1.000) forwards;
    background-color: white;
    padding: 20px;
    border-radius: 15px;
    max-width: 600px;
    margin-right: 7px;
    margin-left: 7px;
    width: 500px;
    text-align: center;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .modal-content-review input {
    width: 100%;
    height: 150px;
    margin-top: 10px;
    margin-bottom: 10px;
    border: 2px solid #2c3e50;
    border-radius: 15px;
    padding: 15px;
  }

  .modal-content-review input:focus {
    animation: inputAnimation 1s;
    animation-fill-mode: forwards;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .modal-content-review button {
    border: none;
    width: 100%;
    padding: 5px;
    border-radius: 15px;
    background-color: #2c3e50;
    color: white;
    cursor: pointer;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .modal-content-review button:hover {
    background-color: #525b64;
  }

  @keyframes moveUp {
    0% {
      transform: scale(0);
      opacity: 0;
    }

    50% {
      border-radius: 15px;
    }

    90% {
      border-radius: 15px;
    }

    100% {
      transform: scale(1);
      opacity: 1;
    }
  }

  @keyframes inputAnimation {
    0% {
      outline: none;
    }

    50% {
      outline: 2px solid white;
    }

    100% {
      outline: 3px solid #2c3e50;
    }
  }

  p {
    margin-top: 5px;
    margin-bottom: 5px;
  }

  .modal-content button {
    margin-top: 10px;
    background-color: #2c3e50;
    width: 10rem;
    border: none;
    padding: 7px;
    color: white;
    border-radius: 15px;
    cursor: pointer;
    margin-right: 5px;
    margin-left: 5px;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  .modal-content button:hover {
    background-color: #525b64;
  }

  .video-container {
    position: relative;
    width: 100%;
    height: 10em;
    /* padding-bottom: 56.25%; 16:9 aspect ratio */
  }

  .video-container iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    border-radius: 15px;
  }

  .watched {
    display: none;
  }

  .lovethis {
    display: none;
  }
  .pagination {
    display: flex;
    justify-content: center;
    background-color: #2c3e50;
    color: white;
    padding: 15px;
  }
  .pagination button, span {
    margin-right: 5px;
    margin-left: 5px;
    padding: 5px;
    border-radius: 15px;
    border: none;
    cursor: pointer;
  }
  .pagination button {
    width: 8em;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }

  #video-background {
    position: fixed;
    top: 110px;
    right: 80px;
    bottom: 0;
    /* min-width: 100%;
            min-height: 100%; */
    margin-top: -20px;
    width: 5em;
    height: auto;
    z-index: 1;
    border-radius: 15px;
    box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
  }
</style>