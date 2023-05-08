<template>
  <div>
    <!-- Label for the area selector -->
    <label for="area">Select Area:</label>
    <!-- Select input for choosing an area -->
    <select v-model="selectedArea" @change="getWeatherData">
      <!-- Use v-for to loop through the areas array and generate options for each area -->
      <option v-for="(area, index) in areas" :key="index" :value="area.name">{{ area.name }}</option>
    </select>

    <!-- Display a loading message if the areas data is still loading -->
    <div v-if="loadingArea">
      Loading areas...
    </div>

    <!-- Display the current weather data for the selected area -->
    <h2>Weather Information for {{ selectedArea }}</h2>

    <!-- Display a loading message if the weather data is still loading -->
    <div v-if="loadingWeather">
      Loading weather...
    </div>

    <!-- Display the weather data for the selected area -->
    <div v-if="!loading && weatherData">
      <p>Data: {{getCurrentDate()}}</p>
      <ul>
        <li>Weather: {{weatherData.weather[0].main}}</li>
        <li>Temperature: {{ weatherData.main.temp }} &deg;C</li>
        <li>Humidity: {{ weatherData.main.humidity }}%</li>
        <li>Wind Speed: {{ weatherData.wind.speed }} km/h</li>
      </ul>
    </div>

    <!-- Display rentals information for the selected holiday and area -->
    <h2>Rentals information for {{ selectedHoliday }} in {{ selectedArea }}</h2>

    <!-- Display a loading message if the rental data is still loading -->
    <div v-if="loadingHotel">
      Loading rental information...
    </div>

    <!-- Display the hotel data for the selected holiday and area -->
    <div v-if="selectedHoliday && hotelData && !loadingHotel">
      <ul>
        Use v-for to loop through the hotelData array and generate a list item for each hotel
        <li v-for="(hotel, index) in hotelData" :key="index">
          <h3>{{ hotel.name }}</h3>
          <li>address: {{ hotel.address }}<li>
          <li>Price: {{ hotel.RateInfo.ChargeableRateInfo["@total"] }}<li>
          <li>rating: {{hotel.rating}}<li>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
// Import the axios library
import axios from "axios";

// Define the areaSelector component and export it as the default
export default {
  // Declare the props that the component accepts
  props: {
    selectedCountry: {
      type: String,
      required: true,
    },
    selectedHoliday: {
      type: String,
      default: null,
    },
  },
  // Define the component's data
  data() {
    return {
      selectedArea: "",
      areas: [],
      weatherData: null,
      rentalData: null,
      loadingWeather: false,
      loadingArea: false,
      loadingHotel: false,
    };
  },
  // Define the component's methods
  methods: {
    // Define an async method for obtaining areas for the selected country
    async getAreas() {
      try {
        // Set the loadingArea flag to true
        this.loadingArea = true;
        // Send a GET request to the OpenAQ API for locations in the selected country
        const response = await axios.get(
          `https://api.openaq.org/v1/locations?country=${this.selectedCountry}`
        );
        // Extract the areas from the response data
        const areas = response.data.results;
        // If there are areas, map them to objects with name and value properties
        if (areas && areas.length) {
          this.areas = areas.map((area) => ({
            name: area.city || area.location,
            value: area.city || area.location,
          }));
          // Set the selectedArea to the first area in the list
          this.selectedArea = areas[0].city || areas[0].location;
        }
        // Set the loadingArea flag to false
        this.loadingArea = false;
      } catch (error) {
        console.error(error);
      }
    },
    // Define an async method for obtaining weather data for the selected area
    async getWeatherData() {
      try {
        // Set the loadingWeather flag to true
        this.loadingWeather = true;
        // Log a message to the console indicating that we're beginning to obtain weather data
        console.log('Begin to obtain the weather data:', this.selectedArea);
        // Send a GET request to the OpenWeatherMap API for weather data for the selected area
        const response = await axios.get(
          `https://api.openweathermap.org/data/2.5/weather?q=${this.selectedArea}&units=metric&appid=bc7882743af6c02ccf49bae35af9b9ee`
        );
        // Store the weather data in the component's data
        this.weatherData = response.data;
        // Set the loadingWeather flag to false
        this.loadingWeather = false;
      } catch (error) {
        // Log any errors to the console
        console.error(error);
      } 
    },
    // Define an async method for obtaining hotel data for the selected area
    async getHotelData() {
      try {
        this.loadingHotel = true;
        const today = new Date().toISOString().slice(0, 10); // Get current date in yyyy-mm-dd format
        console.log('Begin to obtain the hotel data:', this.selectedArea, today);

        // construct URL with the selected area and Google Maps API key
        const url = `https://maps.googleapis.com/maps/api/place/textsearch/json?query=hotels+in+${this.selectedArea}
                    &key=AIzaSyBSWzkFatU2pkNDS-Ljg8GO6oxj1tiW4XY${this.selectedHoliday.date}`;

        // send GET request to the Google Places API with the constructed URL
        const response = await axios.get(url);

        // filter the hotel data to only include hotels with USD currency
        const hotelData = response.data.HotelList.filter(hotel => {
          return hotel.RateInfo.ChargeableRateInfo["@currencyCode"] === "USD";
        });

        // update loading state and set the hotelData state variable to the filtered hotel data
        this.loadingHotel = false;
        this.hotelData = hotelData;

      } catch (error) {
        console.error(error);
      }
    },

    //the function used to obtain the current date
    getCurrentDate(){
      let d = new Date();
      let months = ["January","February","March","April",
      "May","June","July","August","September","October",
      "November" ,"December"];
      let days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
      
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth( )];
      let year = d.getFullYear( );
      return `${day} ${date} ${month} ${year}`;
    },
  },

  //excute the following functions when the props selectedCountry, selectedArea and selectedHoliday are changed.
  watch: {
  selectedCountry() {
    this.getAreas();
  },
  selectedArea() {
    this.getWeatherData();
    this.getHotelData();
  },
  selectedHoliday() {
    // Define the method to obtain the new info of hotel data when the selected holiday changed.
    if (this.selectedHoliday) {
      this.loadingHotel = true;
      // construct URL with the selected area and Google Maps API key
      const url = `https://maps.googleapis.com/maps/api/place/textsearch/json?query=hotels+in+${this.selectedArea}
                    &key=AIzaSyBSWzkFatU2pkNDS-Ljg8GO6oxj1tiW4XY${this.selectedHoliday.date}`;
      // send GET request to the Google Places API with the constructed URL and obtain the data
      axios.get(url).then((response) => {
        this.rentalData = response.data.results;
        this.loadingHotel = false;
      }).catch((error) => {
        console.error(error);
      });
    } else {
      this.rentalData = null;
    }
  },
},
  mounted() {
    this.getAreas();
  },
};
</script>