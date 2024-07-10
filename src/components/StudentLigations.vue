<template>
  <v-form ref="studentLigationsForm">
    <v-card title="Student Ligations" style="width: 900px; max-height: 600px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
      <v-card-text style="padding-top: 0px;">
        <v-row>
          <v-col cols="12" md="12" style="display: flex; align-items: center; justify-content: left; padding-top: 0px; padding-bottom: 0px;">
            <v-data-table :headers="headers" :items="studentLigations" height="400"></v-data-table>
          </v-col>
        </v-row>
      </v-card-text>

      <v-card-actions style="display: flex; justify-content: center; padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
        <v-btn text="Close" variant="text" @click="$emit('close')">Close</v-btn>
        <v-btn text="Download" variant="text" @click="download">Download</v-btn>
      </v-card-actions>
    </v-card>
  </v-form>
</template>

<script>
import axios from 'axios';

export default {
  props: {
    backendUrl: {
      type: String,
      required: true
    }
  },
  data() {
    return {
      headers: [
        { title: 'Group Name', align: 'start', key: 'groupName' },
        { title: 'Students', align: 'end', key: 'students' },
        { title: 'Date', align: 'end', key: 'date' },
        { title: 'Sequence', align: 'end', key: 'sequence' },
      ],
      studentLigations: []
    };
  },
  created() {
    this.querySimulatedLigation();
  },
  methods: {
    async download() {
      try {
        const response = await axios.post(`${this.backendUrl}/download_student_ligations`, {
          responseType: 'blob',
        });
        const blob = new Blob([response.data], { type: 'text/csv' });
        const link = document.createElement('a');
        link.href = window.URL.createObjectURL(blob);
        link.download = 'student_ligations.csv';
        link.click();
      } catch (error) {
        console.error('Error downloading student ligations:', error);
      }
    },

    async querySimulatedLigation() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_simulated_ligation`);
        this.studentLigations = response.data.studentLigations;
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },
  }
};
</script>
