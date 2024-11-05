<template>
    <div>
        <form @submit.prevent="fetch_remote_data">
            
            <div class="form-group">
                <h2> Weather Data </h2>
                <p> Please fill in the form and use the submit button to load data.</p>
                <label for="">Location</label>
                <br>
                <!-- The input field for the location, bound to the data_inputted.location property -->
                <input class="form-control rounded" v-model="form_input.location" type="text" required />
            </div>
            
            <div class="form-group">
                <label for="">Start Date</label>
                <br>
                <!-- The input field for the start date, bound to the data_inputted.start_dt property -->
                <input class="form-control rounded" type="date" v-model="form_input.start_dt" required />
            </div>
            
            <div class="form-group">
                <label for="">End Date</label>
                <br>
                
                <input class="form-control rounded" type="date" v-model="form_input.end_dt" required />
            </div>
            <br>
            <div>
                <button class="btn btn-danger" type="submit">Submit</button>
            </div>
        </form>
        <br /><br />

        <div v-if = "dataLoaded">
            <Bar :data="chart_data" :options="chart_options" />
        </div>

        <div v-if = "dataLoaded">
            <hr>
            <h5> Weather Data in a Table</h5>
            <table class="table">
                <thead>
                    <tr>
                        <th v-for="adate in keys">{{ adate }}</th>
                    </tr>
                </thead>
                <tbody>
                    <tr>
                        <td v-for="atemp in values">{{ atemp }}</td>
                    </tr>
                </tbody>
            </table>            
        </div>

    </div>
</template>

<style>
table, th, td {
  border: 1px solid black;
  border-collapse: collapse;
}

</style>
<script setup>
import axios from "axios";

import {
  Chart as ChartJS,
  Title,
  Tooltip,
  Legend,
  BarElement,
  CategoryScale,
  LinearScale
} from 'chart.js'
import { Bar } from 'vue-chartjs'

ChartJS.register(CategoryScale, LinearScale, BarElement, Title, Tooltip, Legend)
import { ref } from 'vue'

// converted form_input
// for these variables i just matched with the previous options code and reformatted
const form_input = ref({
  start_dt: "",
  end_dt: "",
  location: "",
});

// converted chart_options - for bar chart configuration
const chart_options = ref({
  responsive: true,
  maintainAspectRatio: true,
  scales: {
    x: {
      title: {
        display: true,
        text: "Time",
      },
    },
    y: {
      title: {
        display: true,
        text: "Temperature (F)",
      },
    },
  },
});

// converted chart_data to composition API
const chart_data = ref({
  labels: [],
  datasets: [
    {
      label: "Average Temperature",
      backgroundColor: "#f87979",
      data: [],
    },
  ],
});

const dataLoaded = ref(false);
const keys = ref([]);
const values = ref([]);

// converted call to api
const fetch_remote_data = async () => {
  const options = {
    method: 'GET',
    url: 'https://weatherapi-com.p.rapidapi.com/history.json',
    params: {
      q: form_input.value.location,
      dt: form_input.value.start_dt,
      end_dt: form_input.value.end_dt,
      lang: 'en',
    },
    headers: {
      'X-RapidAPI-Key': '6c6f790650msh5ce4da2fced77a7p1b1776jsn247d9f531b74',
      'X-RapidAPI-Host': 'weatherapi-com.p.rapidapi.com',
    },
  };

  try {
    const resp = await axios.request(options);
    const w_data = resp.data.forecast.forecastday;

    // gets the data from the form boxes
    keys.value = w_data.map((item) => item.date);
    values.value = w_data.map((item) => item.day.avgtemp_f);

    // updates chart
    chart_data.value = {
      labels: keys.value,
      datasets: [
        {
          label: "Average Temperature",
          backgroundColor: "#f87979",
          data: values.value,
        },
      ],
    };
    dataLoaded.value = true;
  } catch (error) {
    console.error(error);
  }
};
</script>
<!--
ChatGPT was used for this exam to help find errors and improve code
prompt used: "can you fix the errors and improve this code. It should be in composition API <code>"

on last exam i got a zero because npm run dev did not work/run. I think i was able to solve it
this time around. I was able to run npm run dev and got the web app worked perfectly

Last exam when i ran npm install, i got some vulnerabilities and ran npm audit fix, and i think that messed up
the exam. This time i just ran it one time and left the vulnerabilities alone.
-->

