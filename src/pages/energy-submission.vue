<template>
  <v-container>
    <v-card class="pa-4">
      <v-card-title class="text-h5">Energy Usage Input</v-card-title>
      <v-card-text>
        <v-form ref="form">
          <!-- Date Picker -->
          <v-text-field
            label="Date"
            v-model="date"
            type="date"
            :rules="[rules.required]"
            outlined
          ></v-text-field>

          <!-- Energy Consumption Input -->
          <v-text-field
            label="Energy Usage (kWh)"
            v-model="usage"
            type="number"
            min="0"
            step="0.1"
            :rules="[rules.required, rules.numeric]"
            outlined
          ></v-text-field>
        </v-form>
      </v-card-text>

      <v-card-actions>
        <v-btn color="primary" @click="submitData">Submit</v-btn>
      </v-card-actions>
    </v-card>

    <!-- Snackbar for feedback -->
    <v-snackbar v-model="snackbar" :timeout="3000" color="success">
      {{ message }}
      <template v-slot:actions>
        <v-btn color="white" text @click="snackbar = false">Close</v-btn>
      </template>
    </v-snackbar>
  </v-container>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

// Reactive variables
const date = ref("");
const usage = ref("");
const snackbar = ref(false);
const message = ref("");

// Validation rules
const rules = {
  required: (value) => !!value || "This field is required",
  numeric: (value) => !isNaN(parseFloat(value)) || "Must be a number",
};

// Function to submit data
const submitData = async () => {
  if (!date.value || !usage.value) {
    message.value = "Please fill in all fields.";
    snackbar.value = true;
    return;
  }

  try {
    const response = await axios.post(
      "https://hxnynv34i6.execute-api.us-east-1.amazonaws.com/prod/energy/input",
      {
        date: date.value,
        usage: parseFloat(usage.value),
      }
    );
    message.value = "Energy usage recorded successfully!";
  } catch (error) {
    console.error("API Error:", error);
    message.value = "Failed to submit data.";
  }
  snackbar.value = true;
};
</script>

<style scoped>
.v-container {
  max-width: 500px;
  margin: auto;
  margin-top: 20px;
}
</style>