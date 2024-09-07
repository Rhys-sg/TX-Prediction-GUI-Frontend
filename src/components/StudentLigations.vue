<template>
  <v-card title="Student Ligations" style="width: 900px; height: 600px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
    <!-- Student Ligations Component -->
    <!-- 
      This component queries and displays a table of student ligations and provides functionality to download the data as a CSV file. 
      The table is populated with data fetched from a backend API, and the CSV download feature is triggered by a button click.
      The form does not handle submissions/insertions but provides options to close the modal or download the data.
    -->
    <v-card-text>
      <v-row>
        <v-col cols="12" md="4">
          <v-combobox
            v-model="selectedSchool"
            :items="schools"
            label="School"
            @change="fetchTermsBySchool"
          ></v-combobox>
        </v-col>
        
        <v-col cols="12" md="4">
          <v-combobox
            v-model="selectedTerm"
            :items="terms"
            label="Term/Section"
            :disabled="selectedSchool === ''"
          ></v-combobox>
        </v-col>

        <template v-if="selectedSchool !== '' && selectedTerm !== ''">
          <v-col cols="12" md="12">
            <v-data-table
              :headers="headers"
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
      headers: [
        { title: 'Order Name', align: 'start', key: '0' },
        { title: 'Sequence', align: 'end', key: '1' },
        { title: 'Date', align: 'end', key: '2' },
        { title: 'Students', align: 'end', key: '3' },
      ],
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
        this.fetchTermsBySchool();
      }
    }
  },
  created() {
    this.fetchSchools();
  },
  methods: {
    async fetchSchools() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_schools`);
        this.schools = response.data.schools;
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    async fetchTermsBySchool() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_terms_by_school`, {
          school: this.selectedSchool,
        });
        this.terms = response.data.terms || [];
      } catch (error) {
        console.error('An error occurred while fetching terms.', error);
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
      const headers = this.headers.map(header => header.title);
      const rows = this.studentLigations.map(ligation => 
        this.headers.map(header => ligation[header.key]).join(',')
      ).join('\n');

      const csvContent = `data:text/csv;charset=utf-8,${headers.join(',')}\n${rows}`;
      const encodedUri = encodeURI(csvContent);
      const link = document.createElement('a');
      link.setAttribute('href', encodedUri);
      link.setAttribute('download', 'student_ligations.csv');
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  }
};
</script>

