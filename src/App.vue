<script setup>
import { useVuelidate } from "@vuelidate/core";
import {
  required,
  email,
  minLength,
  helpers,
  numeric,
} from "@vuelidate/validators";
import { reactive } from "vue";

const form = reactive({
  email: "",
  password: "",
  confirmPassword: "",
  birthYear: "",
});
const currentYear = new Date().getFullYear();

const rules = {
  email: {
    required: helpers.withMessage("Email is required.", required),
    email: helpers.withMessage("Please enter a valid email.", email),
  },
  password: {
    required: helpers.withMessage("Password is required.", required),
    minLength: helpers.withMessage(
      "Password must be at least 8 characters.",
      minLength(8)
    ),
    strongPassword: helpers.withMessage(
      "Password must include both letters and numbers.",
      helpers.regex(/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/)
    ),
  },
  confirmPassword: {
    required: helpers.withMessage("Please confirm your password.", required),
    matchesPassword: helpers.withMessage(
      "Passwords do not match.",
      (value) => value === form.password
    ),
  },
  birthYear: {
    required: helpers.withMessage("Year of birth is required.", required),
    numeric: helpers.withMessage("Year of birth must be a number.", numeric),
    maxYear: helpers.withMessage(
      `Year of birth cannot be greater than ${currentYear}.`,
      (value) => value <= currentYear
    ),
  },
};

const v$ = useVuelidate(rules, form);

const handleSubmit = () => {
  v$.value.$touch();
  if (!v$.value.$invalid) {
    alert("Form submitted successfully!");
    console.log("Form Data:", form);
  } else {
    console.log("errors:", v$.value);
  }
};
</script>

<template>
  <main>
    <div class="form-container">
      <form @submit.prevent="handleSubmit" novalidate>
        <div class="form-group">
          <label class="email-label" for="email">Email</label>
          <input
            id="email"
            v-model="form.email"
            type="text"
            placeholder="Enter your email"
            :class="{ invalid: v$.email.$error }"
          />
          <span class="email-error" v-if="v$.email.$error">{{
            v$.email.$errors[0].$message
          }}</span>
        </div>

        <div class="form-group">
          <label for="password">Password</label>
          <input
            id="password"
            v-model="form.password"
            type="password"
            placeholder="Enter your password"
            :class="{ invalid: v$.password.$error }"
          />
          <span v-if="v$.password.$error">{{
            v$.password.$errors[0].$message
          }}</span>
        </div>

        <div class="form-group">
          <label for="confirmPassword">Confirm Password</label>
          <input
            id="confirmPassword"
            v-model="form.confirmPassword"
            type="password"
            placeholder="Confirm your password"
            :class="{ invalid: v$.confirmPassword.$error }"
          />
          <span v-if="v$.confirmPassword.$error">{{
            v$.confirmPassword.$errors[0].$message
          }}</span>
        </div>

        <div class="form-group">
          <label for="birthYear">Year of Birth</label>
          <input
            id="birthYear"
            v-model="form.birthYear"
            type="number"
            placeholder="Enter your year of birth"
            :class="{ invalid: v$.birthYear.$error }"
          />
          <span v-if="v$.birthYear.$error">{{
            v$.birthYear.$errors[0].$message
          }}</span>
        </div>

        <button type="submit" class="submit-button">Submit</button>
      </form>
    </div>
  </main>
</template>

<style scoped>
.form-container {
  max-width: 400px;
  margin: 50px auto;
  padding: 29px;
  border-radius: 15px;
  background: linear-gradient(130deg, #fbfbfb, #e6e6e6);
  box-shadow: -15px 10px 8px #d1d1d1, -4px -4px 8px #ffffff;
  border: 1px solid #00000036;
  position: relative;
}

.form-group {
  margin-bottom: 20px;
  display: flex;
  flex-direction: column;
}

label {
  font-size: 14px;
  font-weight: 600;
  color: #333;
  margin: 8px 0;
}

input {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 8px;
  font-size: 14px;
  transition: border-color 0.3s ease, box-shadow 0.3s ease;
}
.email-label {
  margin-top: 0;
}

input:focus {
  border-color: #118b50;
  box-shadow: 0 0 2px #4caf5080;
}

input.invalid {
  border-color: #f53b57;
}

span {
  color: #f53b57;
  font-size: 12px;
  margin-top: 76px;
  position: absolute;
}
.email-error {
  margin-top: 68px;
}

.submit-button {
  padding: 12px;
  width: 100%;
  border: none;
  background: #139958;
  color: #fff;
  border-radius: 8px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-top: 12px;
}

.submit-button:hover {
  background: #118b50;
}
</style>
