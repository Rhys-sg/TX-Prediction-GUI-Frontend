<template>
  <v-card title="Student Ligations" style="width: 900px; height: 600px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
    <!-- Student Ligations Component -->
    <!-- 
      This component queries and displays a table of student ligations and provides functionality to download the data as a CSV file. 
      The table is populated with data queryed from a backend API, and the CSV download feature is triggered by a button click.
      The form does not handle submissions/insertions but provides options to close the module or download the data.

      Student Ligations are queried by school and term, selected using a dropdown <v-combobox> object.
    -->
    <v-card-text>
      <v-row>
        <v-col cols="12" md="4">
          <v-combobox
            v-model="selectedSchool"
            :items="schools"
            label="School"
            @change="queryTermsBySchool"
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

        <template v-if="selectedSchool !== '' && selectedTerm !== ''">
          <v-col cols="12" md="12">
            <v-data-table
              :items="studentLigations"
              fixed-header
              height="295"
            ></v-data-table>
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

export default {
  props: {
    backendUrl: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      studentLigations: [],
      selectedSchool: '',
      selectedTerm: '',
      schools: [],
      terms: []
    };
  },
  watch: {
    selectedTerm(newVal, oldVal) {
      if (newVal !== oldVal && this.selectedTerm !== '' && this.selectedSchool !== '') {
        this.querySimulatedLigation();
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
        console.error('An error occurred.', error);
      }
    },

    async queryTermsBySchool() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_terms_by_school`, {
          school: this.selectedSchool,
        });
        this.terms = response.data.terms || [];
      } catch (error) {
        console.error('An error occurred while querying terms.', error);
        this.terms = [];
      }
    },

    async querySimulatedLigation() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_simulated_ligation`, {
          school: this.selectedSchool,
          term: this.selectedTerm
        });
        this.studentLigations = response.data.studentLigations;
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    download() {
      if (this.studentLigations.length === 0) {
        alert("No data to download");
        return;
      }

      const csvContent = this.convertToCSV(this.studentLigations);
      const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
      const link = document.createElement('a');
      const url = URL.createObjectURL(blob);

      link.setAttribute('href', url);
      link.setAttribute('download', 'student_ligations.csv');
      link.style.visibility = 'hidden';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    },

    convertToCSV(data) {
      const headers = Object.keys(data[0]).join(',') + '\n';
      const rows = data.map(obj => Object.values(obj).join(',')).join('\n');
      return headers + rows;
    },
  }
};
</script>
