<template>
  <v-form >
    <v-card title="User Account Placeholder" style="width: 500px; max-height: 600px; top: 50%; left: 50%; transform: translate(-50%, 0%);">
      <v-card-text>
        
      </v-card-text>

      <v-card-actions style="display: flex; justify-content: center; padding-top: 0px; padding-bottom: 12px; padding-right: 22px;">
        <v-btn text="Log out" variant="text" @click="$emit('log-out')">Log out</v-btn>
        <v-btn text="Close" variant="text" @click="$emit('close')">Close</v-btn>
      </v-card-actions>
    </v-card>
  </v-form>
</template>

<script>
</script>