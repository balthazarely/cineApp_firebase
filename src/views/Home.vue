<template>
  <section>
    <div class="hero main-container">
      <div class="movie-theatre-wrapper">
        <h1
          class="title is-4 is-size-5-mobile has-text-white has-text-weight-bold"
        >
          Welcome to CineApp
          <p
            class="subtitle is-6 is-size-6-mobile has-text-white"
            v-if="isLoggedIn == true"
          >
            signed in as {{ currentUser }}
          </p>
        </h1>

        <div class="search-wrapper">
          <input
            style="max-width: 100%"
            class="input is-rounded search-input"
            type="text"
            placeholder="Search for a movie"
            v-on:keyup.enter="getMovieByTitle"
            v-model="movieSearch"
            label="Standard"
          />~
          <!-- <button class="button is-rounded search-btn">Search</button> -->
        </div>
      </div>
    </div>
    <div v-if="!hasSearchHappened">
      <div class="second-container">
        <div class="hero container">
          <div
            class="columns is-centered mt-5 mb-5 ml-5 mr-5 has-text-center-mobile"
          >
            <div class="column">
              <p
                class="title is-4 is-size-4-mobile is-size-4-tablet has-text-centered-mobile has-text-left-tablet "
              >
                Welcome to CineApp
              </p>
              <div class="columns">
                <div class="column">
                  <p
                    class="subtitle is-6 is-size-6-mobile is-size-6-tablet has-text-left"
                  >
                    The goal of this app is for users to be able to browse,
                    search, and add movies to their watchlist, hopefully solving
                    the decision paralysis that we experience when scrolling
                    through the netflix homepage. By saving movies to your
                    watchlist, you'll have an easy reference for later.
                  </p>
                </div>
                <div class="column">
                  <p
                    class="subtitle is-6 is-size-6-mobile is-size-6-tablet has-text-left"
                  >
                    Once you've watched your movie, move it over to the journal,
                    where you can rate, add notes, and chart when you watch it.
                    Review your journal when asked for a new movie
                    recommendation! All movie data was created using
                    <a target="BLANK" href="https://www.themoviedb.org/"
                      >The Movie Database's</a
                    >
                    API.
                  </p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="top-movie-container">
        <div class="hero container">
          <div class="columns  ml-5 mr-5">
            <div class="column">
              <p class="title is-4 has-text-left pt-5 has-text-white">
                Top Movies
              </p>
              <carousel
                :perPageCustom="[
                  [500, 4],
                  [666, 5],
                  [700, 6],
                  [900, 7],
                  [1218, 8],
                ]"
                class="carousel"
              >
                <slide
                  class="carousel-slide"
                  v-for="movie in topMoviesData.results"
                  :key="movie.id"
                >
                  <img
                    class="movie-cover"
                    v-bind:src="
                      'https://image.tmdb.org/t/p/w200/' + movie.poster_path
                    "
                  />

                  <router-link
                    :to="{
                      name: 'Movie',
                      params: { movie_id: movie.id },
                    }"
                  >
                    <p class="title is-7 is-multiline has-text-white">
                      {{ movie.title }}
                    </p>
                  </router-link>
                </slide>
              </carousel>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="container" v-if="hasSearchHappened">
      <div class="notification is-white">
        <div class="columns">
          <div class="columns is-multiline  is-mobile  is-variable is-3">
            <div
              class="column movie-text-container is-one-fifth-widescreen is-one-third-mobile is-one-fifth-desktop is-one-quarter-tablet  movie-card"
              v-for="movie in movieData.results"
              :key="movie.id"
            >
              <figure class="image movie-image is-2-by-3">
                <router-link
                  :to="{
                    name: 'Movie',
                    params: { movie_id: movie.id },
                  }"
                >
                  <img
                    class="movie-cover"
                    v-bind:src="
                      'https://image.tmdb.org/t/p/w200/' + movie.poster_path
                    "
                  />
                </router-link>

                <div class="movie-rating">{{ movie.vote_average }}</div>
              </figure>
              <div class="title is-6 is-size-6-mobile movie-title">
                <router-link
                  :to="{
                    name: 'Movie',
                    params: { movie_id: movie.id },
                  }"
                >
                  {{ movie.title }}
                </router-link>
              </div>
              <div class="subtitle is-6 is-size-7-mobile movie-subtitle">
                <!-- {{ trimeDate(movie.release_date) }} -->
              </div>
            </div>
          </div>
        </div>
      </div>
      <nav
        class="pagination"
        role="navigation"
        aria-label="pagination"
        v-if="hasSearchHappened"
      >
        <a
          class="pagination-previous"
          title="This is the first page"
          :disabled="previousDisabled"
          @click="previousPage"
          >Previous</a
        >
        <a class="pagination-next" @click="nextPage" :disabled="nextDisabled"
          >Next page</a
        >
      </nav>
    </div>
  </section>
</template>

<script>
import "bulma/css/bulma.css";
import axios from "axios";
import firebase from "firebase";
import db from "@/firebase/init";
import { Carousel, Slide } from "vue-carousel";
export default {
  name: "Home",
  components: {
    Carousel,
    Slide,
  },
  data() {
    return {
      hasSearchHappened: false,
      movieSearch: "",
      includeAdult: false,
      movieData: [],
      topMoviesData: [],
      topMovieData: [],
      errFeedback: null,
      apiKey: "5e9bd2fa585826bdfc4233fb6424f425",
      currentPage: 1,
      totalPages: null,
      user: null,
      isLoggedIn: false,
      currentUser: false,
    };
  },
  created() {
    this.getUser();
    axios
      .get(
        `https://api.themoviedb.org/3/movie/top_rated?api_key=${this.apiKey}&language=en-US&page=1`
      )
      .then((response) => {
        this.topMoviesData = response.data;
        console.log(this.topMoviesData, "< top movies");
      });
  },
  methods: {
    logout() {
      firebase
        .auth()
        .signOut()
        .then(() => {
          this.$router.push("/login");
        });
    },
    getUser() {
      if (firebase.auth().currentUser) {
        this.isLoggedIn = true;
        this.currentUser = firebase.auth().currentUser.email;
      }
    },
    getMovieByTitle() {
      axios
        .get(
          `https://api.themoviedb.org/3/search/movie?api_key=${this.apiKey}&language=en-US&page=${this.currentPage}&include_adult=${this.includeAdult}&query=${this.movieSearch}`
        )
        .then((response) => {
          if (response.data.Error) {
            this.movieData = null;
            this.errFeedback = "Your search didn't yeild any results";
          } else {
            this.movieData = response.data;
            this.totalPages = response.data.total_pages;
            this.hasSearchHappened = true;
            console.log(this.totalPages);
            this.errFeedback = "";
            console.log(response.data);
          }
        });
    },

    nextPage() {
      this.currentPage++;
      this.getMovieByTitle();
    },
    previousPage() {
      this.currentPage--;
      this.getMovieByTitle();
    },
    trimeDate(date) {
      let length = date.length;
      let wantedLength = 6;
      let math = length - wantedLength;
      return date.slice(0, math);
    },
  },
  computed: {
    previousDisabled() {
      if (this.currentPage == 1) {
        return true;
      } else {
        return false;
      }
    },
    nextDisabled() {
      if (this.currentPage == this.totalPages) {
        return true;
      } else {
        return false;
      }
    },
  },
};
</script>

<style lang="sass">
.notification a:not(.button):not(.dropdown-item)
  text-decoration: none

.movie-text-container
  position: relative

.movie-image
  position: relative
  left: -5px

.info-container
  background-position: center
  background-repeat: no-repeat
  background-size: cover
  filter: grayscale(70%)
  background-image: linear-gradient(to left, rgba(255, 255, 255 , .6), rgba(255, 255, 255 , .9)),  url('../assets/blackkklansman.jpg')
  height: 250px

.top-movie-container
  background: #012844

.second-container
  background-position: top
  background-repeat: no-repeat
  background-size: cover
  filter: grayscale(70%)
  background-image: linear-gradient(rgba(255, 255, 255 , .9), rgba(255, 255, 255 , .9)),  url("../assets/pulpfiction.jpg")
  width: 100%

.movie-title
  text-align: left
  margin-top: 10px
  bottom: 0

.movie-subtitle
  text-align: left
  margin-top: 2px

.fav-movie-cover
  height: 60px
  width: 40px
  img
    width: 100%
    height: 100%

.movie-card
  position: relative
  &:hover
    .add-movie-btn
       opacity: 1

.add-movie-btn
  position: absolute
  top: 22px
  right: 22px
  z-index: 10
  opacity: 0

.movie-rating
  background-image: linear-gradient(to right, #3ABAC0, #1ca5a1)
  width: 40px
  height: 40px
  display: flex
  justify-content: center
  align-items: center
  color: white
  position: absolute
  z-index: 10
  right: 0px
  bottom: 0px

.main-container
  background: white



.movie-theatre-wrapper
  width: 100%
  height: 400px
  background-position: center
  background-repeat: no-repeat
  background-size: cover
  background-image: linear-gradient(rgba(1, 40, 68 , .6), rgba(1, 40, 68 , .6)),  url("../assets/blackkklansman.jpg")
  display: flex
  justify-content: center
  align-items: center
  flex-direction: column
  position: relative

.search-wrapper
  display: flex
  flex-direction: row
  width: 80vw
  justify-content: center
  position: relative

.search-input
  position: absolute
  width: 80vw


.search-btn
  position: absolute
  right: 0px
  background-image: linear-gradient(to right, #3ABAC0, #1ca5a1)
  border: none
  color: white
  &:hover
      color: rgba(1, 40, 68 , 1)

label
    display: block

input, label
    margin: .4rem 0
</style>
