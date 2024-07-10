<template>
  <v-form 
    ref="ligateForm"
    @submit.prevent="ligateSubmit()"
  >
    <v-card title="Ligate" style="width: 800px; max-height: 550px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
      <v-card-text style="padding-top: 12px;">

        <v-row>
          <v-col cols="12" md="4" style="padding-bottom: 0px;">
            <v-combobox
              :items="schools"
              label="School"
            ></v-combobox>
          </v-col>
          
          <v-col cols="12" md="4" style="padding-bottom: 0px;">
            <v-combobox
              :items="semesterSection"
              label="Semester/Section"
            ></v-combobox>
          </v-col>

          <v-col cols="12" md="4" style="padding-bottom: 0px;">
            <v-text-field
              v-model="internalGroupName"
              label="Group Name"
              :rules="groupNameRules"
              rows="1"
              variant="filled"
              required
            ></v-text-field>
          </v-col>
        </v-row>

        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
            <v-text-field
              v-model="internalLigateStudents"
              label="Students"
              :rules="studentsRules"
              rows="1"
              variant="filled"
              required
            ></v-text-field>
          </v-col>
        </v-row>
        
        <v-row>
          <v-col cols="12" md="12" style="padding-bottom: 0px;">
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
        </v-row>
      </v-card-text>

      <v-card-actions style="padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
        <v-btn text="Close" variant="text" @click="$emit('close')">Close</v-btn>
        <v-btn text="Submit" type="submit"></v-btn>
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
    schools: {
      type: Array,
      required: true
    },
    semesterSection: {
      type: Array,
      required: true
    },
    groupName: {
      type: String,
      required: true
    },
    ligateStudents: {
      type: String,
      required: true
    },
    currentPromoterSequence: {
      type: String,
      required: true
    },
  },
  data() {
    return {
      internalGroupName: this.groupName,
      internalLigateStudents: this.ligateStudents,
      internalCurrentPromoterSequence: this.currentPromoterSequence,
      groupNameRules: [
        v => !!v || 'Group name is required',
      ],
      studentsRules: [
        v => !!v || 'Students are required',
      ],
      codingStrandRules: [
        v => !!v || 'Coding strand is required',
      ],
    };
  },
  watch: {
    internalGroupName(newVal) {
      this.$emit('update:groupName', newVal);
    },
    internalLigateStudents(newVal) {
      this.$emit('update:ligateStudents', newVal);
    },
    internalCurrentPromoterSequence(newVal) {
      this.$emit('update:currentPromoterSequence', newVal);
    }
  },
  methods: {
    async ligateSubmit() {
      const { valid } = await this.$refs.ligateForm.validate();
      if (valid) {
        this.insertSimulatedLigation();
        this.$emit('ligateSubmit');
      }
    },

    async insertSimulatedLigation() {
      try {
        const response = await axios.post(`${this.backendUrl}/insert_simulated_ligation`, {
          groupName: this.internalGroupName,
          students: this.internalLigateStudents,
          codingStrand: this.currentPromoterSequence,
        })
        console.log(response.data.entries);
        console.log(response.data.success);
        console.log(response.data.error);
      } catch (error) {
        console.error('An error occurred.', error)
      }
    },
  }
};
</script>
