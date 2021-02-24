<template>
  <div class="post-index">
    <!-- header -->
    <Header />
    <!-- end header -->

    <!-- main content -->
    <header class="pt-5 border-bottom bg-light">
      <div class="container pt-md-1 pb-md-1">
        <h1 class="bd-title mt-4 font-weight-bold">
          <i class="fa fa-bell" aria-hidden="true"></i> AGENDA
        </h1>
        <p class="bd-lead">Agenda terbaru tentang SMK Indonesia.</p>
      </div>
    </header>

    <!-- breadcrumb -->
    <nav aria-label="breadcrumb">
      <ol class="breadcrumb">
        <li class="breadcrumb-item">
          <router-link :to="{ name: 'home' }" class="text-decoration-none"
            ><i class="fa fa-home"></i> Home
          </router-link>
        </li>
        <li class="breadcrumb-item">
          <router-link :to="{ name: 'event' }" class="text-decoration-none"
            ><i class="fa fa-bell"></i> Agenda
          </router-link>
        </li>
      </ol>
    </nav>
    <!-- end breadcrumb -->

    <div class="container-fluid mt-5 mb-5">
      <div class="row">
        <div v-if="events.length > 0" class="row">
          <div class="col-md-6 mb-3" v-for="event in events" :key="event.id">
            <router-link
              :to="{ name: 'detail_event', params: { slug: event.slug } }"
              class="text-decoration-none text-dark"
            >
              <div class="card mb-3 shadow-sm border-0">
                <div class="card-body">
                  <h6>{{ event.title }}</h6>
                  <hr />
                  <div>
                    <i class="fa fa-map-marker" aria-hidden="true"></i>
                    {{ event.location }}
                  </div>
                  <div class="mt-2">
                    <i class="fa fa-calendar" aria-hidden="true"></i>
                    {{ event.date }}
                  </div>
                </div>
              </div>
            </router-link>
          </div>
        </div>

        <div v-else>
          <div class="row">
            <div
              class="col-md-6 mb-3"
              v-for="loader in events_loader"
              :key="loader"
            >
              <div class="card border-0 shadow-sm rounded-lg">
                <div class="card-body">
                  <FacebookLoader />
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
import { FacebookLoader } from "vue-content-loader";

//import axios
import axios from "axios";

//import component
import Header from "@/components/Header";
import Footer from "@/components/Footer";
export default {
  name: "EventIndex",
  components: {
    FacebookLoader,
    Header,
    Footer,
  },
  mounted() {
    this.getEvents();
  },
  methods: {
    getEvents() {
      axios
        .get("/api/event")
        .then((response) => {
          this.events = response.data.data.data;
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
        .get("/api/event?page=" + this.nextPage)
        .then((response) => {
          if (response.data.data.current_page < response.data.data.last_page) {
            this.moreExists = true;
            this.nextPage = response.data.data.current_page + 1;
          } else {
            this.moreExists = false;
          }
          response.data.data.data.forEach((data) => {
            this.events.push(data);
          });
        })
        .catch((response) => {
          console.log(response);
        });
    },
  },
  data() {
    return {
      //define properties
      events: [],
      //define content loader data
      events_loader: 4,
      //pagination
      moreExists: false,
      nextPage: 0,
    };
  },
};
</script>