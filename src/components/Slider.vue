<template>
    <!-- slider section -->
    <div id="myCarousel" class="carousel slide" data-bs-ride="carousel">
        <div class="carousel-inner">
            <div v-if="sliders.length > 0">
                <div class="carousel-item" v-for="(slider, id) in sliders" :class="{ active: id==0 }" :key='slider.id'>
                    <img :src="slider.image" class="w-100"
                        style="height:400px;object-fit:cover">
                </div>
            </div>
            <div v-else class="mt-5">
                <div class="card border-0 shadow-sm rounded-lg mb-3" v-for="loader in sliders_loader" :key="loader">
                    <div class="card-body pt-4">
                        <ContentLoader />
                    </div>
                </div>
            </div>
        </div>
        <a class="carousel-control-prev" href="#myCarousel" role="button" data-bs-slide="prev">
            <span class="carousel-control-prev-icon" aria-hidden="true"></span>
            <span class="sr-only">Previous</span>
        </a>
        <a class="carousel-control-next" href="#myCarousel" role="button" data-bs-slide="next">
            <span class="carousel-control-next-icon" aria-hidden="true"></span>
            <span class="sr-only">Next</span>
        </a>
    </div>
    <!-- end slider section -->
</template>

<script>
    //import content loader
    import {
        ContentLoader
    } from 'vue-content-loader'

    //import axios
    import axios from 'axios'

    export default {
        name: 'Slider',
        components: {
            //loader component
            ContentLoader
        },
        data() {
            return {
                sliders: [],
                //define content loader data
                sliders_loader: 1
            }
        },
        created() {
            axios.get('/api/slider').then(response => {
                this.sliders = response.data.data.data;
            });
        }
    }
</script>