<template>
  <div class="post-index">
    <!-- header -->
    <Header />
    <!-- end header -->

    <!-- main content -->
    <header class="pt-5 border-bottom bg-light">
      <div class="container pt-md-1 pb-md-1">
        <h1 class="bd-title mt-4 font-weight-bold">
          <i class="fa fa-image" aria-hidden="true"></i> GALERI
        </h1>
        <p class="bd-lead">Galeri Foto terbaru tentang SMK Indonesia.</p>
      </div>
    </header>

    <!-- breadcrumb -->
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item">
          <router-link :to="{ name: 'home' }" class="text-decoration-none"
            ><i class="fa fa-home"></i> Home</router-link
          >
        </li>
        <li class="breadcrumb-item">
          <router-link :to="{ name: 'photo' }" class="text-decoration-none"
            ><i class="fa fa-image"></i> Galeri Foto</router-link
          >
        </li>
      </ol>
    </nav>
    <!-- end breadcrumb -->

    <div class="container-fluid mt-5 mb-5">
      <div class="row">
        <div v-if="photos.length > 0" class="row">
          <div class="col-md-4 mb-4" v-for="photo in photos" :key="photo.id">
            <div class="card h-100 shadow-sm border-0 rounded-lg">
              <div class="card-img">
                <img
                  :src="photo.image"
                  class="w-100"
                  style="
                    height: 300px;
                    object-fit: cover;
                    border-top-left-radius: 0.3rem;
                    border-top-right-radius: 0.3rem;
                  "
                />
              </div>
              <div class="card-body text-center">
                <h6>{{ photo.caption }}</h6>
              </div>
            </div>
          </div>
        </div>

        <div v-else>
          <div class="row">
            <div
              class="col-md-4 mb-3"
              v-for="loader in photos_loader"
              :key="loader"
            >
              <div class="card border-0 shadow-sm rounded-lg">
                <div class="card-body p-2">
                  <ContentLoader />
                </div>
              </div>
            </div>
          </div>
        </div>

        <div class="text-center" v-show="moreExists">
          <button
            type="button"
            class="btn btn-primary btn-md"
            v-on:click="loadMore"
          >
            <span class="fa fa-arrow-down"></span> LIHAT LEBIH BANYAK
          </button>
        </div>
      </div>
    </div>
    <!-- main content -->

    <!-- footer -->
    <Footer />
    <!-- end footer -->
  </div>
</template>

<script>
//import content loader
import { ContentLoader } from "vue-content-loader";

//import axios
import axios from "axios";

//import component
import Header from "@/components/Header";
import Footer from "@/components/Footer";

export default {
  name: "PhotoIndex",
  components: {
    //loader component
    ContentLoader,
    // component app
    Header,
    Footer,
  },
  data() {
    return {
      //define properties
      photos: [],
      //define content loader data
      photos_loader: 6,
      //pagination
      moreExists: false,
      nextPage: 0,
    };
  },
  mounted() {
    this.getPhotos();
  },
  methods: {
    getPhotos() {
      axios
        .get("/api/photo")
        .then((response) => {
          this.photos = response.data.data.data;
          if (response.data.data.current_page < response.data.data.last_page) {
            this.moreExists = true;
            this.nextPage = response.data.data.current_page + 1;
          } else {
            this.moreExists = false;
          }
        })
        .catch((response) => {
          console.log(response);
        });
    },
    //load more function
    loadMore() {
      axios
        .get("/api/photo?page=" + this.nextPage)
        .then((response) => {
          if (response.data.data.current_page < response.data.data.last_page) {
            this.moreExists = true;
            this.nextPage = response.data.data.current_page + 1;
          } else {
            this.moreExists = false;
          }
          response.data.data.data.forEach((data) => {
            this.photos.push(data);
          });
        })
        .catch((response) => {
          console.log(response);
        });
    },
  },
};
</script>