<template>
  <div class="twubric-manager">
    <h1 style="padding-left: 33%; background-color: #4172f5aa">
      Twubric Twitter Follower Manager
    </h1>
    <div v-if="loading" class="text-gray-600">Loading...</div>
    <div v-else>
      <div style="display: flex; padding-left: 33%; padding-top: 2%">
        <div class="mb-4" style="padding-right: 10px">
          <label class="block mb-2">Date Filter:</label>
          <input type="date" v-model="dateFilter" class="border rounded px-2 py-1" />
        </div>
        <div class="mb-4">
          <label class="block mb-2">Sort By:</label>
          <select v-model="sortBy" class="border rounded px-2 py-1">
            <option value="fullname">Name</option>
            <option value="twubric.total">Twubric Score</option>
            <option value="twubric.friends">Friends</option>
            <option value="twubric.influence">Influence</option>
            <option value="twubric.chirpiness">Chirpiness</option>
          </select>
        </div>
        <div class="mb-4">
            <button class="reset" @click="resetFilter()">Reset</button>
        </div>
      </div>

      <div
        class="outerdiv"
        style="
          display: grid;
          grid-template-columns: repeat(3, 1fr);

          grid-auto-rows: auto;

          grid-gap: 1rem;
        "
      >
        <div v-for="follower in filteredFollowers" :key="follower.uid" class="card">
          <div class="img">
            <img :src="follower.image" :alt="follower.username" />
          </div>
          <div class="infos">
            <div class="name">
              <h2>{{ follower.username }}</h2>
            </div>
            <ul class="twubric">
              <li>
                <h3>{{ follower.twubric.friends }}</h3>
                <h4>friends</h4>
              </li>
              <li>
                <h3>{{ follower.twubric.influence }}</h3>
                <h4>influence</h4>
              </li>
              <li>
                <h3>{{ follower.twubric.chirpiness }}</h3>
                <h4>chirpiness</h4>
              </li>
            </ul>
            <div class="links" style="display: flex; justify-content: space-between">
              <div class="date">{{ formatDate(follower.join_date) }}</div>
              <div>
                <button class="view" @click="removeFollower(follower.uid)">Remove</button>
              </div>
            </div>
          </div>
          <div class="twScore">
            <div class="score" style="padding-right: 20px">
              <h2>{{ follower.twubric.total }}</h2>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "TwubricManager",
  data() {
    return {
      loading: true,
      followers: [],
      dateFilter: "",
      sortBy: "fullname",
    };
  },
  computed: {
    filteredFollowers() {
      let filtered = this.followers;
      if (this.dateFilter) {
        const filterDate = new Date(this.dateFilter).getTime() / 1000;
        filtered = filtered.filter((follower) => {
          return follower.join_date == filterDate;
        });
      }

      return filtered.sort((a, b) => {
        switch (this.sortBy) {
          case "fullname":
            return a.fullname.localeCompare(b.fullname);
          case "twubric.total":
            return a.twubric.total - b.twubric.total;
          case "twubric.friends":
            return a.twubric.friends - b.twubric.friends;
          case "twubric.influence":
            return a.twubric.influence - b.twubric.influence;
          case "twubric.chirpiness":
            return a.twubric.chirpiness - b.twubric.chirpiness;
          default:
            return 0;
        }
      });
    },
  },

  mounted() {
    fetch(
      "https://gist.githubusercontent.com/pandemonia/21703a6a303e0487a73b2610c8db41ab/raw/82e3ef99cde5b6e313922a5ccce7f38e17f790ac/twubric.json"
    )
      .then((response) => response.json())
      .then((data) => {
        this.followers = data;
        this.loading = false;
      })
      .catch((error) => {
        console.error("Error fetching data:", error);
        this.loading = false;
      });
  },
  methods: {
    removeFollower(uid) {
      this.followers = this.followers.filter((follower) => follower.uid !== uid);
    },
    formatDate(timestamp) {
      const date = new Date(timestamp * 1000);
      return date.toLocaleDateString();
    },
    resetFilter() {
            this.dateFilter = "";
        }
  },
};
</script>

<style scoped>
@import url("https://fonts.googleapis.com/css2?family=Poppins:wght@100;300;700&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "Poppins", sans-serif;
  align-items: center;
  justify-content: center;
  background-color: #ade5f9;
  min-height: 100vh;
}
.header {
  align-content: center;
}
img {
  max-width: 100%;
  display: block;
}

ul {
  list-style: none;
}

.card::after,
.card img {
  border-radius: 50%;
}

body,
.card,
.twubric {
  display: flex;
}

.card {
  padding: 2.5rem 2rem;
  border-radius: 10px;
  background-color: rgba(255, 255, 255, 0.5);
  max-width: 500px;
  box-shadow: 0 0 30px rgba(0, 0, 0, 0.15);
  margin: 1rem;
  position: relative;
  transform-style: preserve-3d;
  overflow: hidden;
}

.card::before,
.card::after {
  content: "";
  position: absolute;
  z-index: -1;
}

.card::before {
  width: 100%;
  height: 100%;
  border: 1px solid #fff;
  border-radius: 10px;
  top: -0.7rem;
  left: -0.7rem;
}

.card::after {
  height: 15rem;
  width: 15rem;
  background-color: #4172f5aa;
  top: -8rem;
  right: -8rem;
  box-shadow: 2rem 6rem 0 -3rem #fff;
}

.card img {
  width: 8rem;
  min-width: 80px;
  box-shadow: 0 0 0 5px #fff;
}

.date {
  font-size: 12px;
  display: flex;
  justify-content: space-between;
}

.infos {
  margin-left: 1.5rem;
}

.name {
  margin-bottom: 1rem;
  
}

.name h2 {
  font-size: 1.3rem;
}

.name h4 {
  font-size: 0.8rem;
  color: #333;
}

.text {
  font-size: 0.9rem;
  margin-bottom: 1rem;
}

.twubric {
  margin-bottom: 1rem;
}

.twubric li {
  min-width: 5rem;
}

.twubric li h3 {
  font-size: 0.99rem;
}

.twubric li h4 {
  font-size: 0.75rem;
}

.links button {
  font-family: "Poppins", sans-serif;
  min-width: 120px;
  padding: 0.5rem;
  border: 1px solid #222;
  border-radius: 5px;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.25s linear;
}

.links .follow,
.links .view:hover {
  background-color: #222;
  color: #fff;
}

.links .view,
.links .follow:hover {
  background-color: transparent;
  color: #222;
}

@media screen and (max-width: 450px) {
  .card {
    display: block;
    margin-top: 15px;
  }

  .infos {
    margin-left: 0;
    margin-top: 1.5rem;
  }

  .links button {
    min-width: 100px;
  }

  .link {
    display: flex;
    justify-content: space-between;
    align-items: center;
    place-content: center;
  }
}
</style>
