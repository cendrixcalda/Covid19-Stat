<template>
  <div id="app">
    <section class="search">
      <input
        type="text"
        name="search"
        id="search-bar"
        placeholder="Search for country"
        v-model="query"
        @keypress="getDataPerCountry"
      />
    </section>
    <section class="country-details" v-if="Object.keys(data).length && !errorMessage">
      <p id="country-name">{{ data.country }}</p>
      <p id="date">{{ data.updated | dateFormat }}</p>
    </section>
    <section class="stat" v-if="Object.keys(data).length && !errorMessage">
      <div class="box">
        <label class="label">Total Cases</label>
        <h1 class="figure">{{ data.cases | numberFormat }}</h1>
      </div>
      <div class="box">
        <label class="label">Active Cases</label>
        <h1 class="figure">{{ data.active | numberFormat }}</h1>
      </div>
      <div class="box">
        <label class="label">Total Deaths</label>
        <h1 class="figure">{{ data.deaths | numberFormat }}</h1>
      </div>
      <div class="box">
        <label class="label">Total Recoveries</label>
        <h1 class="figure">{{ data.recovered | numberFormat }}</h1>
      </div>
    </section>
    <p class="no-data" v-else>{{ errorMessage }}</p>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      base_url: "https://corona.lmao.ninja/v2/countries/",
      query: "",
      data: {},
      errorMessage: null,
    };
  },
  methods: {
    getDataPerCountry(e) {
      if (e.key == "Enter" && this.query) {
        fetch(`${this.base_url}${this.query}`)
          .then((response) => {
            this.errorMessage = null;
            let data = response.json();

            if (!response.ok) {
              const error = (data && data.message) || response.statusText;
              return Promise.reject(error);
            }

            return data;
          })
          .then((data) => {
            this.data = data;
          })
          .catch((error) => {
            this.errorMessage = error ? error : "No data from this country";
          });
      }
    },
  },
  filters: {
    dateFormat: function (sec) {
      let date = new Date(sec);
      date = date.toGMTString().split(" ").slice(0, 4).join(" ");

      return date;
    },
    numberFormat: function (figure) {
      return figure.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ",");
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Anton&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Anton", sans-serif;
  background: url("./assets/corona-bg.jpg") no-repeat top center fixed;
  background-size: cover;
  height: 100vh;
  width: 100vw;
}

#app {
  min-height: 100%;
  width: 100%;
  padding: 20px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 100, 255, 0.8),
    rgba(0, 100, 255, 0.1)
  );
}

.search {
  width: 80%;
  margin-bottom: 30px;
}

#search-bar {
  width: 100%;
  padding: 20px;
  outline: none;
  border: none;
  border-radius: 15px;
}

#search-bar:focus {
  transition: 0.3s;
  box-shadow: 0 0 3px 6px rgba(0, 0, 0, 0.1);
  border-radius: 20px;
}

.country-details {
  padding: 20px;
  color: white;
}

.country-details #country-name {
  font-size: 35px;
}

.country-details #date {
  font-size: 20px;
  font-weight: 100;
}

.stat {
  width: 100%;
  display: flex;
  justify-content: center;
  flex-wrap: wrap;
}

.box {
  min-width: 200px;
  padding: 20px;
  margin: 7px;
  border-radius: 10px;
  background: rgba(255, 255, 255, 0.76);
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.figure {
  font-size: 35px;
}

.no-data {
  margin: 30px 20px;
  padding: 10px;
  font-size: 25px;
  color: white;
}
</style>
