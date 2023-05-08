<template>
  <div>
    <!-- Render the CountrySelector component and listen to the 'country-selected' event -->
    <country-selector @country-selected="onCountrySelected" />
    <!-- Render the PublicHolidays and AreaSelector components if a country is selected -->
    <div v-if="selectedCountry">
      <public-holidays :selected-country="selectedCountry" @holiday-selected="onHolidaySelected" />
      <area-selector :selected-country="selectedCountry" :selected-holiday="selectedHoliday" />
    </div>
  </div>
</template>

<script>
//import the three components to this parent component
import CountrySelector from './components/CountrySelector.vue';
import PublicHolidays from './components/PublicHolidays.vue';
import AreaSelector from './components/AreaSelector.vue';

export default {
  components: {
    CountrySelector,
    PublicHolidays,
    AreaSelector,
  },
  data() {
    return {
      selectedCountry: null,
      selectedHoliday: null,
    };
  },
  methods: {
    // Called when a country is selected, updates selectedCountry 
    onCountrySelected(country) {
      this.selectedCountry = country;
      this.selectedHoliday = null;
    },
     // Called when a holiday is selected, updates selectedHoliday
    onHolidaySelected(holiday) {
      this.selectedHoliday = holiday;
    },
  },
};
</script>