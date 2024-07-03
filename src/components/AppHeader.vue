<template>
  <v-app-bar app>
    <v-container>
      <v-row class="align-center">
        <!-- Left-aligned content -->
        <v-col class="d-flex" cols="auto" style="margin-left: -128px;">
          <v-toolbar-title>Dirty Cell Slury</v-toolbar-title>
        </v-col>

        <!-- Centered content -->
        <v-col class="d-flex justify-center" cols="auto">
          <v-btn text>Tools</v-btn>
          <v-btn text>Classroom</v-btn>
          <v-btn @click="$emit('new-input')">New Input</v-btn>
        </v-col>

        <!-- Right-aligned content -->
        <v-col class="d-flex justify-end" cols="auto" style="margin-right: -128px;">
          <v-btn @click="$emit('user-login')" icon="mdi-account" size="small"></v-btn>
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
  name: 'AppHeader',
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
