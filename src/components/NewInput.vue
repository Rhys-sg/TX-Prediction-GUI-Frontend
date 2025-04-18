<template>
  <!-- NewInputForm Component -->
  <!-- 
    This component allows users to submit information about the transcription rate of after observed ligation.
    The form contains multiple student records with associated details such as first name, last name, and email address. 
    Users can dynamically add or remove input fields for students. 
    Additional fields for a coding strand sequence, observed TX rate, and notes are also included.
    The form includes validation for required fields, email format, and domain validity, and submits data to a backend API.
    It also emits events for form submission and closing to track the popup.
  -->
  <v-form 
    ref="newInputForm"
    @submit.prevent="newInputSubmit()"
  >
    <v-card title="New Input" style="width: 800px; max-height: 625px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
      <v-card-text style="padding-bottom: 0px;">
        <v-row>
          <v-col cols="12" md="4">
            <v-combobox
              v-model="selectedSchool"
              :items="schools"
              :rules="schoolRules"
              label="School"
              @change="queryTermsBySchool"
            ></v-combobox>
          </v-col>

          <v-col cols="12" md="4">
            <v-combobox
              v-model="selectedTerm"
              :items="terms"
              :rules="termRules"
              label="Semester/Section"
              :disabled="selectedSchool === ''"
            ></v-combobox>
          </v-col>
        </v-row>

        <h4 style="padding-bottom: 16px;">Students</h4>
        <v-row v-for="(inputSet, index) in internalInputSets" :key="index">

          <v-col cols="12" md="1" class="d-flex align-center">
            <v-btn
              icon="mdi-minus"
              size="small"
              style="transform: translate(0%, -12.5%);"
              @click="removeInputSet(index)"
              :readonly="inputSets.length <= 1"
              :variant="inputSets.length <= 1 ? 'plain' : 'elevated'"
            ></v-btn>
          </v-col>

          <v-col cols="12" md="3" style="padding-bottom: 0px;">
            <v-text-field
              v-model="inputSet.firstname"
              :rules="nameRules"
              label="First name"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4" style="padding-bottom: 0px;">
            <v-text-field
              v-model="inputSet.lastname"
              :rules="nameRules"
              label="Last name"
              required
            ></v-text-field>
          </v-col>

          <v-col cols="12" md="4" style="padding-bottom: 0px;">
            <v-text-field
              v-model="inputSet.email"
              label="Email address"
              :rules="emailRules"
              type="email"
              required
            ></v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="1" class="d-flex align-center">
            <v-btn
              icon="mdi-plus"
              size="small"
              style="transform: translate(0%, -12.5%);"
              @click="addInputSet()"
            ></v-btn>
          </v-col>
        </v-row>

        <v-row>
          <v-container>
            <v-divider></v-divider>
          </v-container>
        </v-row>

        <v-row style="padding-bottom: 12px;">
          <v-col cols="12" md="8">
            <v-text-field
              v-model="internalCurrentPromoterSequence"
              label="Coding Strand"
              :rules="codingStrandRules"
              rows="1"
              variant="filled"
              maxlength="36"
              counter
              required
            ></v-text-field>
          </v-col>
        
          <v-col cols="12" md="4">
            <v-text-field
              v-model="internalInputObservedTX"
              :rules="observedTXRateRules"
              label="Observed Relative Fluorescence"
              required
            ></v-text-field>
          </v-col>
        </v-row>
        
        <v-textarea
          v-model="internalInputNotes"
          label="Notes"
          rows="2"
          variant="filled"
          auto-grow
        ></v-textarea>
      </v-card-text>

      <v-card-actions style="padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
        <v-btn text="Close" variant="text" @click="$emit('close')">Close</v-btn>
        <v-btn text="Submit" type="submit" variant="tonal"></v-btn>
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
    },
    email: {
      type: String,
      required: true
    },
    inputSets: {
      type: Array,
      required: true
    },
    currentPromoterSequence: {
      type: String,
      required: true
    },
    inputObservedTX: {
      type: String,
      required: true
    },
    inputNotes: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      internalInputSets: this.inputSets,
      internalCurrentPromoterSequence: this.currentPromoterSequence,
      internalInputObservedTX: this.inputObservedTX,
      internalInputNotes: this.inputNotes,
      nameRules: [
        v => !!v || 'Name is required',
      ],
      emailRules: [
        v => !!v || 'Email is required',
        v => /.+@.+\..+/.test(v) || 'E-mail must be valid',
        v => this.validateDomain(v) || 'E-mail must have a valid domain',
      ],
      codingStrandRules: [
        v => !!v || 'Coding strand is required',
      ],
      observedTXRateRules: [
        v => !!v || 'Observed TX Rate is required',
      ],
      schoolRules: [
        v => !!v || 'School is required',
      ],
      TermRules: [
        v => !!v || 'Term is required',
      ],
      validDomains: [],
      selectedSchool: '',
      selectedTerm: '',
      schools: [],
      terms: []
    };
  },
  watch: {
    internalInputSets(newVal) {
      this.$emit('update:internalInputSets', newVal);
    },
    internalCurrentPromoterSequence(newVal) {
      this.$emit('update:internalCurrentPromoterSequence', newVal);
    },
    internalInputObservedTX(newVal) {
      this.$emit('update:internalInputObservedTX', newVal);
    },
    internalInputNotes(newVal) {
      this.$emit('update:internalInputNotes', newVal);
    },
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
    this.getValidDomain();
    this.querySchools();
  },
  methods: {
    addInputSet() {
      this.inputSets.push({
        firstname: '',
        lastname: '',
        email: '',
      });
    },

    removeInputSet(index) {
      if (this.inputSets.length > 0) {
        this.inputSets.splice(index, 1);
      }
    },

    async newInputSubmit() {
      const { valid } = await this.$refs.newInputForm.validate();
      if (valid) {
        this.insertObservedTX();
        this.$emit('newInputSubmit');
      }
    },
    async insertObservedTX() {
      try {
        const response = await axios.post(`${this.backendUrl}/insert_observed_TX`, {
          codingStrand: this.internalCurrentPromoterSequence,
          account_email: this.email,
          school: this.selectedSchool,
          term: this.selectedTerm,
          students: this.internalInputSets.map(entry => `${entry.email}: ${entry.firstname} ${entry.lastname}`).join(', '),
          observed_TX: this.internalInputObservedTX,
          notes: this.internalInputNotes,
        })
      } catch (error) {
        console.error('An error occurred.', error)
      }
    },

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

    async getValidDomain() {
      try {
        const response = await axios.post(`${this.backendUrl}/get_valid_domain`);
        this.validDomains = response.data.domains;
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    validateDomain(email) {
      if (!this.validDomains.length) {
        return false;
      }
      let domain = email.split('@')[1];
      return this.validDomains.some(validDomain => domain.endsWith(validDomain));
    }
  },
};
</script>
