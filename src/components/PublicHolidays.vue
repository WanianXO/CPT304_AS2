<template>
  <div>
    <h2>Public Holidays:</h2>
    <!-- Display loading message if data is being fetched -->
    <div v-if="loading">
      Loading holidays...
    </div>

    <!-- Display the list of public holidays if data is fetched -->
    <ul>
      <li v-for="holiday in publicHolidays" :key="holiday.date">
        {{ holiday.date }} - {{ holiday.name }}
      </li>
    </ul>

    <!-- Display dropdown to select a holiday -->
    <label for="holiday-select">Select a holiday:</label>
    <select id="holiday-select" v-model="selectedHoliday" @change="onHolidaySelected">
      <option v-for="(holiday, index) in publicHolidays" :key="index" :value="holiday.name">{{ holiday.name }}</option>
    </select>
  </div>
</template>

<script>
// Declare and export the PublicHolidays component
export default {
  // Declare the selectedCountry prop which is passed to the component from its parent
  props: {
    selectedCountry: {
      type: Object,
      required: true
    }
  },
  // Declare the component's data
  data() {
    return {
      publicHolidays: [],
      selectedHoliday: null,
      loading: false,
    };
  },
  // Call the fetchPublicHolidays method when the component is mounted
  mounted() {
    this.fetchPublicHolidays();
  },
  // Declare the component's methods
  methods: {
    // Define the fetchPublicHolidays method to fetch public holidays for the selected country
    async fetchPublicHolidays() {
      this.loading = true; // Set loading to true
      // Fetch public holidays for the selected country from the Nager.Date API
      const response = await fetch(
        `https://date.nager.at/api/v3/PublicHolidays/2023/${this.selectedCountry}`
      );
      // Parse the response as JSON and set publicHolidays to the parsed data
      this.publicHolidays = await response.json();
      this.loading = false; // Set loading to false
    },
    // Emit a 'holiday-selected' event when a holiday is selected from the dropdown
    onHolidaySelected() {
      this.$emit('holiday-selected', this.selectedHoliday);
    },
  },
  watch: {
  selectedCountry() {
    this.fetchPublicHolidays();
  },
  }
};
</script>