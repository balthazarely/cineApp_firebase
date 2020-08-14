<template>
  <section class="hero main-container ">
    <div class="movie-theatre-wrapper ">
      <p
        class="title is-4 is-size-5-mobile has-text-white has-text-weight-bold"
      >
        Browse by Genre
      </p>
      <div class="form-container" style="display: flex">
        <div class="control">
          <div class="select">
            <select v-model="genreSelected" @change="fetchGenreList">
              <option value="28">Action</option>
              <option value="12">Adventure</option>
              <option value="16">Animation</option>
              <option value="35">Comedy</option>
              <option value="80">Crime</option>
              <option value="99">Documentary</option>
              <option value="18">Drama</option>
              <option value="10751">Family</option>
              <option value="14">Fantasy</option>
              <option value="36">History</option>
              <option value="27">Horror</option>
              <option value="10402">Music</option>
              <option value="9648">Mystery</option>
              <option value="10749">Romance</option>
              <option value="878">Science Fiction</option>
              <option value="10770">TV Movie</option>
              <option value="53">Thriller</option>
              <option value="10752">War</option>
              <option value="37">Western</option>
            </select>
          </div>
        </div>
        <div class="control">
          <div class="select">
            <select v-model="sortBy" @change="fetchGenreList">
              <option value="popularity.desc">Popularity </option>
              <option value="revenue.desc">Revenue </option>
              <option value="vote_average.desc">Vote Average</option>
            </select>
          </div>
        </div>
        <div class="control">
          <div class="select">
            <select v-model="chosenYear" @change="fetchGenreList">
              <option value="">all</option>
              <option value="2020">2020</option>
              <option value="2019">2019</option>
              <option value="2018">2018</option>
              <option value="2017">2017</option>
              <option value="2016">2016</option>
              <option value="2015">2015</option>
              <option value="2014">2014</option>
              <option value="2013">2013</option>
              <option value="2012">2012</option>
              <option value="2011">2011</option>
              <option value="2010">2010</option>
              <option value="2009">2009</option>
              <option value="2008">2008</option>
              <option value="2007">2007</option>
              <option value="2006">2006</option>
              <option value="2005">2005</option>
              <option value="2004">2004</option>
              <option value="2003">2003</option>
              <option value="2002">2002</option>
              <option value="2001">2001</option>
              <option value="2000">2000</option>
              <option value="1999">1999</option>
              <option value="1998">1998</option>
              <option value="1997">1997</option>
              <option value="1996">1996</option>
              <option value="1995">1995</option>
              <option value="1994">1994</option>
              <option value="1993">1993</option>
              <option value="1992">1992</option>
              <option value="1991">1991</option>
              <option value="1990">1990</option>
              <option value="1989">1989</option>
              <option value="1988">1988</option>
              <option value="1987">1987</option>
              <option value="1986">1986</option>
              <option value="1985">1985</option>
              <option value="1984">1984</option>
              <option value="1983">1983</option>
              <option value="1982">1982</option>
              <option value="1981">1981</option>
              <option value="1980">1980</option>
              <option value="1979">1979</option>
              <option value="1978">1978</option>
              <option value="1977">1977</option>
              <option value="1976">1976</option>
              <option value="1975">1975</option>
              <option value="1974">1974</option>
              <option value="1973">1973</option>
              <option value="1972">1972</option>
              <option value="1971">1971</option>
              <option value="1970">1970</option>
            </select>
          </div>
        </div>
      </div>
    </div>
    <div class="container" else>
      <div class="notification is-white">
        <div class="columns">
          <div class="columns is-mobile is-multiline is-variable is-3">
            <div
              class="column  is-one-fifth-widescreen is-one-third-mobile is-one-fifth-desktop is-one-quarter-tablet  movie-card"
              v-for="movie in movieData.results"
              :key="movie.id"
            >
              <router-link
                :to="{
                  name: 'Movie',
                  params: { movie_id: movie.id },
                }"
              >
                <figure class="image movie-image is-2-by-3">
                  <img
                    class="movie-cover"
                    v-bind:src="
                      'https://image.tmdb.org/t/p/w200/' + movie.poster_path
                    "
                  />
                </figure>
              </router-link>
              <div class="title is-6 is-size-4-mobile movie-title">
                <router-link
                  :to="{
                    name: 'Movie',
                    params: { movie_id: movie.id },
                  }"
                >
                  <p
                    class="title is-6 is-size-6-mobile"
                    style="text-decoration: none;"
                  >
                    {{ movie.title }}
                  </p>
                </router-link>
              </div>
              <div class="subtitle is-6 movie-subtitle">
                {{ trimeDate(movie.release_date) }}
              </div>
            </div>
          </div>
        </div>

        <div v-if="genreSelected != null">
          <nav class="pagination" role="navigation" aria-label="pagination">
            <a
              class="pagination-previous"
              title="This is the first page"
              :disabled="previousDisabled"
              @click="previousPage"
              >Previous</a
            >
            <a
              class="pagination-next"
              @click="nextPage"
              :disabled="nextDisabled"
              >Next page</a
            >
          </nav>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
import "bulma/css/bulma.css";
import axios from "axios";
export default {
  name: "genres",
  data() {
    return {
      dropdownOpen: false,
      movieData: [],
      genreSelected: 28,
      chosenYear: 2020,
      currentPage: 1,
      sortBy: "popularity.desc",
      apiKey: "5e9bd2fa585826bdfc4233fb6424f425",
      searchCompleted: false,
    };
  },
  created() {
    this.fetchGenreList();
  },
  methods: {
    fetchGenreList() {
      axios
        .get(
          ` https://api.themoviedb.org/3/discover/movie?api_key=${this.apiKey}&language=en-US&sort_by=${this.sortBy}&include_adult=false&include_video=false&page=${this.currentPage}&with_genres=${this.genreSelected}&primary_release_year=${this.chosenYear}`
        )
        .then((response) => {
          if (response.data.Error) {
            this.movieData = null;
            this.errFeedback = "Your search didn't yeild any results";
          } else {
            this.movieData = response.data;
            this.errFeedback = "";
            console.log(response.data);
            this.searchCompleted = true;
          }
        });
    },
    nextPage() {
      this.currentPage++;
      this.fetchGenreList();
    },
    previousPage() {
      this.currentPage--;
      this.fetchGenreList();
    },
    trimeDate(date) {
      let length = date.length;
      let wantedLength = 6;
      let math = length - wantedLength;
      return date.slice(0, math);
    },

    // toggelDropdown() {
    //   const dropDown = document.querySelector(".dropdown");
    //   if (this.dropdownOpen == false) {
    //     dropDown.classList.add("is-active");
    //     this.dropdownOpen = true;
    //   } else {
    //     dropDown.classList.remove("is-active");
    //     this.dropdownOpen = false;
    //   }
    // },
    // chooseYear(year) {
    //   this.toggelDropdown();
    //   this.chosenYear = year;
    //   this.fetchGenreList();
    // },
    // chooseGenre(genre) {
    //   this.chosenGenre = genre;
    //   this.fetchGenreList();
    // },
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

<style lang="sass" scoped>
.movie-poster
  width: 150px


.movie-theatre-wrapper
  width: 100%
  height: 200px
  background-position: center
  background-repeat: no-repeat
  background-size: cover
  background-image: linear-gradient(rgba(1, 40, 68 , .8), rgba(1, 40, 68 , .8)),  url("../assets/trainspotting.jpg")
  display: flex
  justify-content: center
  align-items: center

.year-wrapper
  width: 100%
  height: 200px
  background-position: top
  background-repeat: no-repeat
  background-size: cover
  background: #F8F8F8
  display: flex
  justify-content: center
  align-items: center
</style>
