<script>
import { RouterView } from 'vue-router'
export default {
  data() {
    return {
      showNavigation : false,
      mobileNav : false,
    }
  },
  methods: {
    showNav() {
      this.showNavigation = true;
      this.mobileNav = true;
    },
    closeNav() {
      this.showNavigation = false;
      this.mobileNav = false;
    }
  }
}
</script>

<template>
  <header>

<center>
      <nav>
        <div>
          <h1>Movie App</h1>
        </div>
        <div class="nav">
        <RouterLink to="/"><p>Home</p></RouterLink>
        <RouterLink to="/watched"><p>Watched Movies</p></RouterLink>
        <RouterLink to="/reviewed"><p>Reviewed Movies</p></RouterLink>
        <RouterLink to="/about"><p>About</p></RouterLink>
      </div>
      <div class="mobile-hamburger" @click="showNav" v-if="showNavigation === false">
        <div class="line"></div>
        <div class="line"></div>
        <div class="line"></div>
      </div>
      <div class="close-nav" v-else @click="closeNav">
        <div><p>X</p></div>
      </div>
        <!-- <HomeView/> -->
      </nav>
      <div class="mobile-nav" v-show="mobileNav">
        <RouterLink to="/"><p>Home</p></RouterLink>
        <hr>
        <RouterLink to="/watched"><p>Watched Movies</p></RouterLink>
        <hr>
        <RouterLink to="/reviewed"><p>Reviewed Movies</p></RouterLink>
        <hr>
        <RouterLink to="/about"><p>About</p></RouterLink>
      </div>
    </center>
  </header>

  <RouterView />
</template>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@500&display=swap');

* {
font-family: "Poppins", sans-serif;
margin: 0;
padding: 0;
box-sizing: border-box;
}
  a {
    text-decoration: none;
  }
  nav {
    display: flex;
    justify-content: space-around;
    align-items: center;
    width: 100%;
    height: 80px;
    text-align: center;
    background-color:#2c3e50;
  }
  h1 {
    color: white;
  }
  .nav {
    display: flex;
    justify-content: space-between;
    width: 25em;
  }
  .mobile-hamburger {
    display: none;
    cursor: pointer;
  }
  .mobile-hamburger .line {
    width: 50px;
    height: 5px;
    background-color: #fff;
    margin-top: 5px;
    margin-right: 5px;
  }
  .close-nav {
    width: 50px;
    height: 50px;
    color: white;
    display: none;
  }
  .close-nav div {
    display: flex;
    justify-content: center;
    align-content: center;
    width: 60px;
    margin-top: 1px;
    background-color: #536474;
    border-radius: 50px;
    cursor: pointer;
  }
  .close-nav div p {
    font-size: 40px;
  }
  .mobile-nav {
    padding: 20px;
    background-color:#2c3e50;
    display: none;
    animation-name: mobile-animation;
    animation-duration: 0.2s;
    transition: ease-in-out;
  }
  @keyframes mobile-animation {
    0% {
      height: 0px;
    }
    100% {
      height: 130px;
    }
  }
  p {
    color: white;
  }
  @media (max-width: 700px) {
    .nav {
      display: none;
    }
    .mobile-hamburger {
      display: block;
    }
    .mobile-hamburger .line {
      animation-name: line-animation;
      animation-duration: 0.5s;
      animation-fill-mode: forwards;
    }
    @keyframes line-animation {
      0% {
        width: 0px;
      }
      100% {
        width: 50px;
      }
    }
    .close-nav {
      display: block;
      animation-name: close-animation;
      animation-duration: 0.3s;
      animation-fill-mode: forwards;
    }
    @keyframes close-animation {
      0% {
        transform: scale(0);
      }
      100% {
        transform: scale(1);
      }
    }

    .mobile-nav {
      display: block;
    }
  }
</style>
