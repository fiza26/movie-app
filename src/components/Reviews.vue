<template>
    <div>
        <br>
        <center>
            <h2 v-show="reviews.length === 0">🙁 No Reviewed Movies</h2>
            <!-- {{ selectedReview.review }} -->
        </center>
        <div class="movie-container">
            <div v-for="review in reviews" :key="review.movie.id">
                <div class="movie-card">
                    <img :src="getFullPosterUrl(review.movie.poster_path)" :alt="review.movie.title"
                        class="movie-poster" v-show="review.review.length < 450"/>
                    <div class="container-item">
                        <h3 class="review-item">{{ review.movie.title }}</h3>
                        <hr>
                        <p class="review-item">{{ review.review }}</p>
                        <input type="textarea" v-model="editedReview" placeholder="Update your review here..."
                            v-show="selectedReview === review">
                        <button @click="editReview(review)" v-if="editReviewState === false ">Edit Review</button>
                        <button @click="deleteReview(review)" v-if="editReviewState === false">Delete
                            Review</button>
                        <button @click="submitEdit(review)" v-if="selectedReview === review">Submit Review</button>
                        <button @click="cancelReview(review)" v-if="selectedReview === review">Cancel</button>
                    </div>
                </div>
                <!-- v-if="review.movie.id === selectedMovie.id" -->
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        props: {
            // review: {
            //     type: String,
            //     required: true,
            // },
            reviews: {
                type: Array,
                required: true
            },
            selectedMovie: {
                type: Object,
                required: true
            }
        },
        data() {
            return {
                selectedReview: null,
                editedReview: '',
                editReviewState: false,
            }
        },
        methods: {
            deleteReview(review) {
                const index = this.reviews.findIndex((r) => r.movie.id === review.movie.id);
                if (index !== -1) {
                    this.reviews.splice(index, 1);
                    localStorage.setItem('reviewedMovies', JSON.stringify(this.reviews));
                }
            },
            editReview(review) {
                this.editReviewState = true;
                this.selectedReview = review;
                console.log(this.selectedReview)
    
            },
            submitEdit(review) {
                const index = this.reviews.findIndex((r) => r.movie.id === review.movie.id);
                if (index !== -1) {
                    this.reviews[index].review = this.editedReview; // Update the review text
                    localStorage.setItem('reviewedMovies', JSON.stringify(this.reviews));
                    this.editReviewState = false; // Reset the editReviewState
                    this.editedReview = '';
                    this.selectedReview = null;
                }
            },
            cancelReview(review) {
                this.editReviewState = false;
                this.selectedReview = null;
            },
            getFullPosterUrl(posterPath) {
                const baseImageUrl = "https://image.tmdb.org/t/p/w500";
                return `${baseImageUrl}${posterPath}`;
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

    .movie-container {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
    }

    .movie-card {
        display: flex;
        background-color: #2c3e50;
        padding: 15px;
        max-width: 700px;
        border-radius: 15px;
        margin: 20px;
        box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
    }

    .movie-poster {
        width: 30%;
        height: auto;
        border-radius: 15px;
        transition: transform 0.3s;
        box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
    }

    .movie-poster:hover {
        transform: scale(1.6);
        border-bottom-right-radius: 15px;
        border-bottom-left-radius: 15px;
    }

    .container-item {
        display: flex;
        flex-direction: column;
        padding: 15px;
        width: 100%;
    }

    /* hr {
        width: 400px;
    } */

    .review-item {
        color: white;
    }

    input {
        /* width: 430px; */
        width: 100%;
        height: 150px;
        margin-top: 10px;
        margin-bottom: 10px;
        text-align: center;
        border-radius: 15px;
        border: none;
    }

    button {
        margin-top: 8px;
        border: none;
        padding: 5px;
        border-radius: 15px;
        cursor: pointer;
        width: 100%;
        box-shadow: rgba(0, 0, 0, 0.16) 0px 3px 6px, rgba(0, 0, 0, 0.23) 0px 3px 6px;
    }

    button:hover {
        background-color: #00D735;
        color: white;
    }

    @media (max-width: 700px) {

        /* CSS rules for screens below 700px */
        /* You can add your styles here */
        .movie-poster {
            /* display: none; */
            max-width: 100%;
            height: 30%;
        }

        /* Add more styles as needed */
    }
</style>