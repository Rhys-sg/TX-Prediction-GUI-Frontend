<template>
  <!-- The application bar component used for NewInput popup, user, settings, and the placeholder buttons -->
  <v-app-bar app>
    <v-container>
      <v-row class="align-center">
        <!-- Left-aligned content -->
        <v-col class="d-flex" cols="auto" style="margin-left: -128px;">
          <v-toolbar-title>E. coli Promoter Prediction</v-toolbar-title>
        </v-col>

        <!-- Centered content -->
        <v-col class="d-flex justify-center" cols="auto">
          <v-btn @click="$emit('student-ligations')">Student Ligations</v-btn>
          <!-- <v-btn @click="$emit('student-observations')">Student Observations</v-btn> -->
          <v-btn @click="$emit('new-input')">New Input</v-btn>
        </v-col>

        <!-- Right-aligned content -->
        <v-col class="d-flex justify-end" cols="auto" style="margin-right: -128px;">
          <v-btn
            v-if="isUserLogedin === false"
            @click="$emit('user-login')"
            icon="mdi-account"
            size="small"
          ></v-btn>
          <v-btn
            v-else
            @click="$emit('user-account')"
            icon=""
            variant="outlined"
            size="small"
          >
            <span><h2>{{ firstName[0] + lastName[0] }}</h2></span>
          </v-btn>
          <v-btn icon="mdi-cog" size="small" style="margin-left: 8px;"></v-btn>
          <v-btn icon="mdi-download" size="small" style="margin-left: 8px;"  @click="takeScreenshot"></v-btn>
        </v-col>
      </v-row>
    </v-container>
  </v-app-bar>
</template>

<script>
import html2canvas from 'html2canvas';

export default {
  props: {
    isUserLogedin: {
      type: Boolean,
      required: true
    },
    firstName: {
      type: String,
      required: true
    },
    lastName: {
      type: String,
      required: true
    },
  },
  methods: {
    async takeScreenshot() {
      const canvas = await html2canvas(document.body);
      const imageData = canvas.toDataURL('image/png');
      const link = document.createElement('a');
      link.href = imageData;
      link.download = 'screenshot.png';
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
    }
  }
}
</script>

<style scoped>
.v-toolbar-title {
  font-weight: bold;
}

.align-center {
  display: flex;
  align-items: center;
}

.align-center > .d-flex {
  flex: 1;
}
</style>
