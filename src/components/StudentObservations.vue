<template>
  <v-card
    title="Student Observations"
    style="width: 900px; height: 600px; position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);"
  >
    <!-- Student Observations Component -->
    <!-- 
      This component queries and displays a table of student Observations and provides functionality to download the data as a CSV file. 
      The table is populated with data queryed from a backend API, and the CSV download feature is triggered by a button click.
      The form does not handle submissions/insertions but provides options to close the module or download the data.

      Student Observations are queried by school and term, selected using a dropdown <v-combobox> object.
    -->
    <v-card-text>
      <v-row>
        <v-col cols="12" md="4">
          <v-combobox
            v-model="selectedSchool"
            :items="schools"
            label="School"
            @change="queryObservations"
          ></v-combobox>
        </v-col>

        <v-col cols="12" md="4">
          <v-combobox
            v-model="selectedTerm"
            :items="terms"
            label="Semester/Section"
            :disabled="selectedSchool === ''"
          ></v-combobox>
        </v-col>

        <v-col cols="12" md="4" class="d-flex align-center">
          <v-btn
            :text="showingGraph ? 'Show Table' : 'Show Graph'"
            variant="text"
            @click="toggleGraph"
            :disabled="!selectedSchool || !selectedTerm"
          ></v-btn>
        </v-col>

        <template v-if="selectedSchool && selectedTerm && !showingGraph">
          <v-col cols="12" md="12">
            <v-data-table
              :items="student_observations"
              fixed-header
              height="295"
            >
              <template v-slot:item.Sequence="{ item }">
                {{ item.Sequence.toUpperCase() }}
              </template>
            </v-data-table>
          </v-col>
        </template>

        <template v-if="showingGraph">
          <v-col cols="12" md="12" style="height: 295px;">
            <StudentObservationsGraph :graph-data="graphData" />
          </v-col>
        </template>
      </v-row>
    </v-card-text>

    <v-card-actions style="display: flex; justify-content: center; padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
      <v-btn text="Close" variant="text" @click="$emit('close')">Close</v-btn>
      <v-btn text="Download" variant="tonal" @click="download">Download</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
import axios from 'axios';
import StudentObservationsGraph from './StudentObservationsGraph.vue';

export default {
  components: {
    StudentObservationsGraph
  },
  props: {
    backendUrl: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      student_observations: [],
      selectedSchool: '',
      selectedTerm: '',
      schools: [],
      terms: [],
      graphData: null,
      showingGraph: false,
    };
  },
  watch: {
    selectedTerm(newVal, oldVal) {
      if (newVal !== oldVal && this.selectedTerm && this.selectedSchool) {
        this.queryObservations();
      }
    },
    selectedSchool(newSchool) {
      if (newSchool) {
        this.queryTermsBySchool();
      }
    }
  },
  created() {
    this.querySchools();
  },
  methods: {
    async querySchools() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_schools`);
        this.schools = response.data.schools;
      } catch (error) {
        console.error('Error fetching schools:', error);
      }
    },
    async queryTermsBySchool() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_terms_by_school`, {
          school: this.selectedSchool,
        });
        this.terms = response.data.terms || [];
      } catch (error) {
        console.error('Error fetching terms:', error);
        this.terms = [];
      }
    },
    async queryObservations() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_observations_by_school_and_term`, {
          school: this.selectedSchool,
          term: this.selectedTerm
        });
        this.student_observations = response.data.student_observations || [];
      } catch (error) {
        console.error('Error fetching observations:', error);
      }
    },
    toggleGraph() {
      if (this.showingGraph) {
        this.showingGraph = false;
      } else {
        this.show_graph();
      }
    },
    async show_graph() {
      try {
        const response = await axios.post(`${this.backendUrl}/get_student_observations_graph_data`, {
          school: this.selectedSchool,
          term: this.selectedTerm
        });
        this.graphData = response.data;
        this.showingGraph = true;
      } catch (error) {
        console.error('Error fetching graph data:', error);
      }
    },
    download() {
      if (this.student_observations.length === 0) {
        alert("No data to download");
        return;
      }
      const csvContent = this.convertToCSV(this.student_observations);
      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
      const link = document.createElement('a');
      const url = URL.createObjectURL(blob);
      link.setAttribute('href', url);
      link.setAttribute('download', 'student_observations.csv');
      link.style.visibility = 'hidden';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    },
    convertToCSV(data) {
      const headers = Object.keys(data[0]).join(',') + '\n';
      const rows = data.map(obj => Object.values(obj).join(',')).join('\n');
      return headers + rows;
    }
  }
};
</script>
