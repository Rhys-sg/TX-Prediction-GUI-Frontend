<template>
  <AppHeader
  :isUserLogedin="isUserLogedin"
  :firstName="firstName"
  :lastName="lastName"
  :email="email"
  @student-ligations="viewStudentLigations"
  @new-input="newInputActive"
  @user-login="userLoginActive"
  @user-account="userAccountActive"/>

  <!-- Center Interface -->
  <v-container class="fill-height d-flex justify-center align-center">
    <v-responsive style="max-width: 868px; height: 438px;">
      <v-card class="py-0 outlined-grey fill-height" variant="outlined">
        <v-row no-gutters class="white-bg upper-height">

          <!-- Content for TOP LEFT cell -->
          <v-col cols="3" class="fill-height">
            <v-card class="py-0 outlined-grey fill-height" variant="outlined" style="position: relative;">
              <img src="../assets/images/GFP.png" style="width: 216px; height: 196px;">
              <div class="text-center" style="
                  position: absolute;
                  left: 55%;
                  top: 50%;
                  transform: translate(-50%, -50%);
                  background-color: white;
                  padding: 5px;
              ">
                <span style="color: #48B560; font-size: 36px;">GFP</span>
              </div>
            </v-card>
          </v-col>

          <!-- Content for TOP CENTER cell -->
          <v-col cols="6" class="fill-height">
            <v-card class="py-0 outlined-grey fill-height" variant="outlined" style="position: relative;">

              <!-- Upper Coding Strand -->
              <div
                v-for="(sequence, index) in upperSequences" 
                :key="index"
                class="bp-text upper-bp-text"
                :style="{ 
                  left: sequence.left || '',
                  right: sequence.right || '',
                  color: sequence.color, transform:
                  sequence.transform || ''
                }"
              >{{ sequence.text }}</div>
              
              <!-- Lower Coding Strand -->
              <div
                v-for="(sequence, index) in lowerSequences" 
                :key="index"
                class="bp-text lower-bp-text"
                :style="{ 
                  left: sequence.left || '',
                  right: sequence.right || '',
                  color: sequence.color, transform:
                  sequence.transform || ''
                }"
              >{{ sequence.text }}</div>
              
              <!-- -10 Label -->
              <div class="text-center h10_h35_background" id="h10_background" style="left: 29.95%; padding: 0px 10.5px;">
                <span class="h10_h35_text" id="h10_text">-10</span>
              </div>

              <!-- -35 Label -->
              <div class="text-center h10_h35_background" id="h35_background" style="left: 71.55%; padding: 0px 10.5px;">
                <span class="h10_h35_text" id="h35_text">-35</span>
              </div>

            </v-card>
          </v-col>

          <!-- Content for TOP RIGHT cell -->
          <v-col cols="3" class="fill-height">
            <v-card class="py-0 outlined-grey fill-height" variant="outlined">
              <img src="../assets/images/RFP.png" style="width: 216px; height: 196px;">
              <div class="text-center" style="
                  position: absolute;
                  left: 45%;
                  top: 50%;
                  transform: translate(-50%, -50%);
                  background-color: white;
                  padding: 5px;
              ">
                <span style="color: #ED0B0B; font-size: 36px;">RFP</span>
              </div>
            </v-card>
          </v-col>
        </v-row>

        <v-row no-gutters class="blue-bg lower-height">
          <!-- Content for BOTTOM LEFT cell -->
          <v-col cols="3" class="fill-height d-flex justify-center align-center">
            <v-card class="py-33 outlined-grey fill-height text-center" variant="outlined" style="align-items: center; padding-top: 33px;">
              
              <v-btn
                text
                class="interface-button"
                @click="cut"
                :readonly="isCut"
                :variant="isCut ? 'outlined' : 'elevated'"
              >Cut</v-btn>

              <v-btn
                text
                class="interface-button"
                @click="insert_default"
                :readonly="!isCut"
                :variant="!isCut ? 'outlined' : 'elevated'"
              >Default</v-btn>

              <v-btn
                text
                class="interface-button"
                @click="predict"
                :readonly="!isCut"
                :variant="!isCut ? 'outlined' : 'elevated'"
              >Predict</v-btn>

              <v-btn
                text
                class="interface-button"
                @click="ligate"
                :readonly="!isCut"
                :variant="!isCut ? 'outlined' : 'elevated'"
              >Ligate</v-btn>
              
            </v-card>
          </v-col>

          <!-- Content for BOTTOM CENTER cell -->
          <v-col cols="6" class="fill-height">
            <v-card class="py-0 outlined-grey fill-height" variant="outlined">

              <img
                id="promoterImage"
                src='../assets/images/promoter_direction_GFP.png'
                style="width: 435 px; height: 60px; left: -1000px; "
              >

              <div class="text-center" style="
                  position: absolute;
                  left: 50%;
                  top: 30px;
                  transform: translate(-50%, -50%);
                  background-color: #C7D2E7;
                  padding: 5px;
              ">
                <span style="color: white; font-size: 36px;">Promoter</span>
              </div>

              <TextInput style="padding-top: 17px;"
                :isCut="isCut"
                :defaultUpdater="defaultUpdater"
                :blankUpdater="blankUpdater"
                @update:localCloneUpperPromoterSequence="handleLocalCloneUpperPromoterSequenceUpdate"
              ></TextInput>
              
            </v-card>
          </v-col>

          <!-- Content for BOTTOM RIGHT cell -->
          <v-col cols="3" class="fill-height">
            <v-card class="py-0 outlined-grey fill-height text-center d-flex justify-center align-center" variant="outlined">

              <v-card
                class="py-1 outlined-grey"
                variant="outlined"
                style="width: 175px; height: 155px; background-color: #FFFFFF;"
              >
                
              <BarChart 
                :predicted_TX="predicted_TX || 0" 
                :observed_TX="observed_TX || 0"
                predicted_TX_color="#747475"
                observed_TX_color="#747475"
              />
              </v-card>
            </v-card>           
          </v-col>
        </v-row>

        <!-- Ligate popup -->
        <v-dialog v-model="isLigateActive">
          <LigateEntry
            :backendUrl="backendUrl"
            v-model:groupName="groupName"
            v-model:ligateStudents="ligateStudents"
            v-model:currentPromoterSequence="currentPromoterSequence"
            @ligateSubmit="ligateSubmit"
            @close="isLigateActive = false"
          />
        </v-dialog>

        <!-- Student Ligation Database -->
        <v-dialog v-model="isViewStudentLigationsActive">
          <StudentLigations
            :backendUrl="backendUrl"
            @close="isViewStudentLigationsActive = false"
          />
        </v-dialog>

        <!-- New input popup -->
        <v-dialog v-model="isNewInputActive">
          <NewInput
            :backendUrl="backendUrl"
            v-model:email="email"
            v-model:inputSets="inputSets"
            v-model:currentPromoterSequence="currentPromoterSequence"
            v-model:inputObservedTX="inputObservedTX"
            v-model:inputNotes="inputNotes"
            @newInputSubmit="newInputSubmit"
            @close="isNewInputActive = false"
          />
        </v-dialog>

        <!-- User Login -->
        <v-dialog v-model="isUserLoginActive" persistent>
          <UserLogin
            :backendUrl="backendUrl"
            v-model:isUserLogedin="isUserLogedin"
            v-model:firstName="firstName"
            v-model:lastName="lastName"
            v-model:email="email"
            v-model:inputSets="inputSets"
          />
        </v-dialog>

        <!-- User Account -->
        <v-dialog v-model="isUserAccountActive">
          <UserAccount
            @log-out="isUserLogedin = false,
            isUserAccountActive = false"
            @close="isUserAccountActive = false"
          />
        </v-dialog>

      </v-card>
    </v-responsive>
  </v-container>

  <AppFooter />
</template>

<script>
import BarChart from './BarChart'
import TextInput from './TextInput'
import LigateEntry from './LigateEntry'
import StudentLigations from './StudentLigations'
import NewInput from './NewInput'
import UserLogin from './UserLogin'
import UserAccount from './UserAccount'
import axios from 'axios'

export default {
  name: 'Interface',
  props: {
    sectionName: String
  },
  data() {
    return {
      backendUrl: 'https://tx-prediction-gui-backend-73jj.onrender.com',

      // Promoter visual
      lines: Array.from({ length: 51 }),
      upstreamUpperOverhang: 'CGAC',
      inSituUpperPromoterSequence: 'TCCGGGCGCTATCATGCCATACCGCGAAAGGTTTTGCACCATTCGT',
      inSituLowerPromoterSequence: 'AGGCCCGCGATAGTACGGTATGGCGCTTTCCAAAACGTGGTAAGCA',
      downstreamLowerOverhang: 'CGCC',
      pCloneUpperPromoterSequence: '',
      pCloneLowerPromoterSequence: '',
      upperSequences: [],
      lowerSequences: [],

      // User interaction
      isCut: false,
      promoterDirectionDegree: 0,

      // Text input
      defaultUpdater: false,
      blankUpdater: true,
      currentPromoterSequence: '',

      // Prediction (and observed)
      predicted_TX: 0,
      observed_TX: 0,
      observedTXEntries: null,
      averageObservedTX: 0,

      // Ligate popup
      isLigateActive: false,

      // View student ligation orders
      isViewStudentLigationsActive: false,

      // New input
      isNewInputActive: false,
      inputSets: [{ firstname: '', lastname: '', email: '' }],
      groupName: '', 
      ligateStudents: '',
      inputObservedTX: '',
      inputNotes: '',

      // User login
      isUserLoginActive: true,
      isUserLogedin: false,
      firstName: '',
      lastName: '',
      email: '',

      // User account
      isUserAccountActive: false,
      
    };
  },
  created() {
    this.upperSequences = [
      { text: 'TTC', left: '0px', color: '#FFA500' },
      { text: this.upstreamUpperOverhang, left: '22px', color: '#747475' },
      { text: this.inSituUpperPromoterSequence, left: '50%', transform: 'translate(-50%, -50%)', color: '#747475' },
      { text: 'GCGG', right: '22px', color: '#FFA500' },
      { text: 'GAA', right: '0px', color: '#FFA500' },
    ];
    this.lowerSequences = [
      { text: 'AAG', left: '0px', color: '#FFA500' },
      { text: 'GCTG', left: '22px', color: '#FFA500' },
      { text: this.inSituLowerPromoterSequence, left: '50%', transform: 'translate(-50%, -50%)', color: '#747475' },
      { text: this.downstreamLowerOverhang, right: '22px', color: '#747475' },
      { text: 'CTT', right: '0px', color: '#FFA500' },
    ];
  },
  watch: {
    isUserLogedin(val) {
      this.isUserLoginActive = !val;
    }
  },
  methods: {
    cut() {
      // Only cut once
      if (this.isCut){
        return;
      }
      this.isCut = true;

      this.update_sequences();
      this.update_h10_h35_labels();
      this.hide_h10_h35();
      this.update_promoter_arrow();      
    },

    update_sequences() {
      // Set in Situ promoter to blank
      this.upstreamUpperOverhang = '';
      this.inSituUpperPromoterSequence = '',
      this.inSituLowerPromoterSequence = '';
      this.downstreamLowerOverhang =  '';

      // Update graphic
      this.upperSequences[1].text = this.upstreamUpperOverhang;
      this.upperSequences[2].text = this.inSituUpperPromoterSequence;
      this.lowerSequences[2].text = this.inSituLowerPromoterSequence;
      this.lowerSequences[3].text = this.downstreamLowerOverhang;
    },

    update_h10_h35_labels() {
      // Flip old -10, -35
      const h10_text = document.getElementById('h10_text');
      const h35_text = document.getElementById('h35_text');
      h10_text.textContent = '-35';
      h35_text.textContent = '-10';

      // Update location for after ligate
      const h10_background = document.getElementById('h10_background');
      const h35_background = document.getElementById('h35_background');
      h10_background.style.top = '33%';
      h35_background.style.top = '33%';
      h10_background.style.left = '24.75%'
      h35_background.style.left = '64.6%';
    },

    hide_h10_h35() {
      const elementsToChange = document.querySelectorAll('.h10_h35_background, .h10_h35_text');
      elementsToChange.forEach(element => element.classList.remove('visible'));
      elementsToChange.forEach(element => element.classList.add('hidden'));
    },

    update_promoter_arrow() {
      var image = document.getElementById('promoterImage');
      image.style.transform = 'rotate(180deg)';
    },

    // Insert pClone-Promoter_lac.pdf promoter
    insert_default() {
      if (!this.isCut) { 
        return; 
      }
      this.pCloneUpperPromoterSequence = 'TTTACACTTTATGCTTCCGGCTCGTATGTTGTGTGG';
      this.defaultUpdater = !this.defaultUpdater;
    },

    handleLocalCloneUpperPromoterSequenceUpdate(newVal) {
      this.currentPromoterSequence = newVal;
    },

    predict () {
      this.makePrediction();
      this.queryObservedTX();
    },
    
    async makePrediction() {
      try {
        const response = await axios.post(`${this.backendUrl}/get_prediction`, {
          codingStrand: this.currentPromoterSequence
        });
        this.predicted_TX = response.data.prediction;
        console.log(this.predicted_TX);
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    async queryObservedTX() {
      try {
        const response = await axios.post(`${this.backendUrl}/query_Observed_TX`, {
          codingStrand: this.currentPromoterSequence
        });
        this.observed_TX = response.data.average_observed_TX;
        console.log(this.observed_TX);
      } catch (error) {
        console.error('An error occurred.', error);
      }
    },

    getAverageFromEntryData(data) {
      let sum = 0;
      for (let i = 0; i < data.length; i++) {
        sum += data[i][2];
      }
      return sum / data.length;
    },

    ligate () {
      this.isLigateActive = true;
    },
    
    viewStudentLigations () {
      this.isViewStudentLigationsActive = true;
    },

    newInputActive () {
      this.isNewInputActive = true;
    },

    async newInputSubmit() {
      this.isNewInputActive = false;
    },

    userLoginActive () {
      this.isUserLoginActive = true;
    },

    // Currently there is no window for user account

    // userAccountActive () {
    //   this.isUserAccountActive = true;
    // },

    async ligateSubmit() {
      this.updateBooleanValues_PostLigate()
      this.showOld1035_PostLigate()
      this.updateGraphicVariables_PostLigate()
      this.updateGraphic_PostLigate() 
    },

    updateBooleanValues_PostLigate() {
      this.isCut = false;
      this.isLigateActive = false;
      this.blankUpdater = !this.blankUpdater;
    },

    showOld1035_PostLigate() {
      const elementsToHide = document.querySelectorAll('.h10_h35_background, .h10_h35_text');
      elementsToHide.forEach(element => element.classList.add('visible'));
    },

    updateGraphicVariables_PostLigate() {
      this.upstreamUpperOverhang = 'CGAC';
      this.inSituUpperPromoterSequence = this.currentPromoterSequence;
      this.inSituLowerPromoterSequence = this.get_complement(this.currentPromoterSequence);
      this.downstreamLowerOverhang =  'CGCC';
    },

    updateGraphic_PostLigate() {
      this.upperSequences[1].text = this.upstreamUpperOverhang;
      this.upperSequences[2].text = this.inSituUpperPromoterSequence;
      this.lowerSequences[2].text = this.inSituLowerPromoterSequence;
      this.lowerSequences[3].text = this.downstreamLowerOverhang;
    },

    get_complement(strand) {
      if (!strand) {
        return '';
      }
      const complement_key = { 'A': 'T', 'T': 'A', 'C': 'G', 'G': 'C' };
      let complements = '';
      for (let i = 0; strand && i < strand.length; i++) {
        complements += complement_key[strand[i]];
      }
      return complements;
    },
  }
};
</script>

<style scoped>
.white-bg {
  background-color: #ffffff;
}
.blue-bg {
  background-color: #C7D2E7;
}
.outlined-grey {
  border: 0px solid #747475 !important;
}
.fill-height {
  height: 100%;
}
.upper-height {
  height: 45%;
}
.lower-height {
  height: 55%;
}
.bp-text {
  position: absolute;
  font: 12.19px "Monaco", monospace, sans-serif;
  letter-spacing: 0.5px;
  font-weight: 550;
  transform: translate(0%, -50%);
}
.upper-bp-text {
  top: 42%;
}
.lower-bp-text {
  top: 58%;
}
.h10_h35_background {
  top: 67%;
  height: 13px;
  position: absolute;
  transform: translate(-50%, -50%);
  background-color: #efefef;
  border-radius: 7px;
  display: flex;
  align-items: center;
  justify-content: center;
}
.h10_h35_text {
  color: #747475;
  font: 12.19px 'Monaco', monospace, sans-serif;
}
.hidden {
  display: none !important;
}
.visible {
  display: block !important;
}
.interface-button {
  width: 175px;
  margin-bottom: 5px;
  font-size: 18px;
  height: 40px;
}
.vertical-line {
  position: absolute;
  top: 50%;
  width: 4px;
  height: 18px;
}
</style>
