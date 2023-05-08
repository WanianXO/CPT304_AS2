<template>
  <div>
    <!-- Label for the country select input -->
    <label for="country-select">Select a country:</label>
    <!-- Select input for choosing the country -->
    <select id="country-select" v-model="selectedCountry" @change="onCountrySelected">
      <!-- Option elements for each country to select from -->
      <option v-for="(option, index) in options" :key="index" :value="option.value">{{ option.label }}</option>
    </select>
  </div>
</template>

<script>
export default {
  data() {
    return {
      selectedCountry: null, // State variable for the selected country
      options: [ // Options for countries to select
        { value: 'us', label: 'United States' },
        { value: 'ca', label: 'Canada' },
        { value: 'gb', label: 'United Kingdom' },
        { value: 'fr', label: 'France' },
        { value: 'de', label: 'Germany' },
        { value: 'au', label: 'Australia' },
        { value: 'it', label: 'Italy' },
        { value: 'cn', label: 'China' },
        { value: 'jp', label: 'Japan' },
        { value: 'es', label: 'Spain' },
        { value: 'br', label: 'Brazil' },
        { value: 'mx', label: 'Mexico' },
        { value: 'ar', label: 'Argentina' },
        { value: 'cl', label: 'Chile' },
        { value: 'co', label: 'Colombia' },
        { value: 'in', label: 'India' },
        { value: 'id', label: 'Indonesia' },
        { value: 'kr', label: 'South Korea' },
        { value: 'my', label: 'Malaysia' },
        { value: 'ph', label: 'Philippines' },
        { value: 'sg', label: 'Singapore' },
        { value: 'th', label: 'Thailand' },
        { value: 'vn', label: 'Vietnam' },
      ],
    };
  },
  methods: {
    onCountrySelected() { // Method to handle the country selection
      this.$emit('country-selected', this.selectedCountry); // Emits an event with the selected country
    },
  },
};
</script>