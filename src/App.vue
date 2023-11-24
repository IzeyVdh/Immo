<template>
  <AppHeader />
  <main>
    <!-- ======= Intro ======= -->
    <section class="c-intro">
      <div class="container">
        <div class="row">
          <div class="col-12">
            <div class="c-intro__border">
              <h1 class="c-intro__title">Panden te koop</h1>
              <span class="u-color-text">De Panne en omgeving</span>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- End Intro -->

    <!-- ======= Search ======= -->
    <section>
      <div class="container mb-4">
        <div class="row">
          <div class="col-lg-3 col-md-6 mb-2">
            <div class="form-group mt-3">
              <label class="form-label" for="gemeente">Gemeentes</label>
              <select class="form-control" id="gemeente" name="city" multiple v-model="filters.city">
                <option v-for="city in filterData.cities" :value="city.city">{{ city.city }} ({{ city.number_of_homes }})</option>
              </select>
            </div>
          </div>
          <div class="col-lg-3 col-md-6 mb-2">
            <div class="form-group mt-3">
              <label class="form-label" for="type">Type</label>
              <select class="form-control" id="type" name="type">
                <option :value="undefined">Maak een keuze...</option>
              </select>
            </div>
          </div>
          <div class="col-lg-3 col-md-6 mb-2 d-flex">
            <div class="c-input form-group mt-3 me-1">
              <label class="form-label" for="prijsmin">Prijs min.</label>
              <input class="form-control" type="number" step="50000" id="prijsmin" name="pricemin" />
            </div>
            <div class="c-input form-group mt-3 ms-1">
              <label class="form-label" for="prijsmax">max.</label>
              <input class="form-control" type="number" step="50000" id="prijsmax" name="pricemax" />
            </div>
          </div>
          <div class="col-lg-3 col-md-6 mb-2">
            <div class="form-group mt-3">
              <label class="form-label" for="opp">Slaapkamers min.</label>
              <select class="form-control" id="opp" name="bedrooms">
                <option :value="undefined">Maak een keuze...</option>
                <option value="1">1</option>
                <option value="2">2</option>
                <option value="3">3</option>
                <option value="4">4</option>
              </select>
            </div>
          </div>
        </div>
      </div>
    </section>
    <!-- End Search -->

    <!-- ======= Property Grid ======= -->
    <section>
      <div class="container">
        <div class="row">
          <div v-if="loading" class="c-loader">
            <div class="c-loader__bar"></div>
            <div class="c-loader__bar"></div>
            <div class="c-loader__bar"></div>
          </div>
          <!-- Start voorbeeld: 1 pand -->
          <div v-else v-for="house in houses" class="col-md-4">
            <div class="c-pand">
              <div class="c-pand__img">
                <img :src="house.photos[0]" :alt="house.name" class="c-pand__img img-fluid" />
              </div>
              <div class="c-pand__overlay">
                <div class="c-pand__content">
                  <div class="c-pand__header">
                    <h2 class="c-pand__title">{{ house.name }}</h2>
                    {{ house.address }}<br />
                    {{ house.city }}
                  </div>
                  <div class="c-pand__body">
                    <div class="pb-1 d-flex">
                      <span class="c-pand__price">&euro; {{ house.price }}</span>
                    </div>
                  </div>
                  <div class="c-pand__footer">
                    <ul class="c-pand__info d-flex justify-content-around">
                      <li>
                        <h4 class="c-pand__label">Opp</h4>
                        <span>{{ house.squarefootage }}m<sup>2</sup></span>
                      </li>
                      <li>
                        <h4 class="c-pand__label">Slpks</h4>
                        <span>{{ house.rooms }}</span>
                      </li>
                      <li>
                        <h4 class="c-pand__label">Badk</h4>
                        <span>{{ house.bathrooms }}</span>
                      </li>
                      <li>
                        <h4 class="c-pand__label">Garage</h4>
                        <span>{{ ifGarage(house.garage) }}</span>
                      </li>
                    </ul>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- End voorbeeld: 1 pand -->
        </div>
      </div>
    </section>
    <!-- End Property Grid -->
  </main>
  <AppFooter />
</template>
<style lang="scss"></style>
<script>
import AppHeader from './components/AppHeader.vue';
import AppFooter from './components/AppFooter.vue';
import { ref } from 'vue';
export default {
  components: {
    AppHeader,
    AppFooter,
  },
  setup() {
    const loading = ref(true);
    const houses = ref([]);

    const filters = ref({
      city: [],
      hometypeid: undefined,
      priceMin: undefined,
      priceMax: undefined,
      bedrooms: undefined,
    });

    const filterData = ref({
      cities: [],
      homeTypes: [],
    });

    const fillFilter = async function () {
      const cities = await fetch(`https://realestate-api.fgmnts.be/api/v1/cities`).then((r) => r.json());
      filterData.value.cities = cities.data;

      const homeTypes = await fetch(`https://realestate-api.fgmnts.be/api/v1/hometypes`).then((r) => r.json());
      filterData.value.homeTypes = homeTypes.data;
    };

    const filterHouses = function () {
      console.log(filters.value);
    };

    const getHouses = async function () {
      const data = await fetch(`https://realestate-api.fgmnts.be/api/v1/homes`).then((r) => r.json());
      houses.value = data.data;
      loading.value = false;
      // console.log(data);
    };
    const ifGarage = function (garage) {
      if (garage === true) {
        return 'Ja';
      } else {
        return 'Nee';
      }
    };

    getHouses();
    fillFilter();

    return {
      loading,
      houses,
      ifGarage,
      filterData,
      filters,
      filterHouses,
    };
  },
};
</script>
