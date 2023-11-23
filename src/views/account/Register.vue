<template>
  <div class="card m-3">
    <h4 class="card-header">Register</h4>
    <div class="card-body">
      <Form
        @submit="onSubmit"
        :validation-schema="schema"
        v-slot="{ errors, isSubmitting }"
      >
        <div class="form-group">
          <label>First Name</label>
          <Field
            name="firstName"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors.firstName }"
          />
          <div class="invalid-feedback">{{ errors.firstName }}</div>
        </div>
        <div class="form-group">
          <label>Last Name</label>
          <Field
            name="lastName"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors.lastName }"
          />
          <div class="invalid-feedback">{{ errors.lastName }}</div>
        </div>
        <div class="form-group">
          <label>Email</label>
          <Field
            name="email"
            type="email"
            class="form-control"
            :class="{ 'is-invalid': errors.email }"
          />
          <div class="invalid-feedback">{{ errors.email }}</div>
        </div>
        <div class="form-group">
          <label>Vehicle type</label>
          <Field
            name="vehicleType"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors.vehicleType }"
          />
          <div class="invalid-feedback">{{ errors.lastName }}</div>
        </div>
        <div class="form-group">
          <label>Base Fare Price</label>
          <Field
            name="baseFarePrice"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors.baseFarePrice }"
          />
          <div class="invalid-feedback">{{ errors.baseFarePrice }}</div>
        </div>
        <div class="form-group">
          <label>Base Fare Distance</label>
          <Field
            name="baseFareDistance"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors.baseFareDistance }"
          />
          <div class="invalid-feedback">{{ errors.baseFareDistance }}</div>
        </div>
        <div class="form-group">
          <label>Username</label>
          <Field
            name="username"
            type="text"
            class="form-control"
            :class="{ 'is-invalid': errors.username }"
          />
          <div class="invalid-feedback">{{ errors.username }}</div>
        </div>
        <div class="form-group">
          <label>Password</label>
          <Field
            name="password"
            type="password"
            class="form-control"
            :class="{ 'is-invalid': errors.password }"
          />
          <div class="invalid-feedback">{{ errors.password }}</div>
        </div>
        <div class="form-group">
          <button class="btn btn-primary" :disabled="isSubmitting">
            <span
              v-show="isSubmitting"
              class="spinner-border spinner-border-sm mr-1"
            ></span>
            Register
          </button>
          <router-link to="login" class="btn btn-link">Cancel</router-link>
        </div>
      </Form>
    </div>
  </div>
</template>

<script setup>
import { Form, Field } from "vee-validate";
import * as Yup from "yup";

import { useUsersStore, useAlertStore } from "@/stores";
import { router } from "@/router";

const schema = Yup.object().shape({
  firstName: Yup.string().required("First Name is required"),
  lastName: Yup.string().required("Last Name is required"),
  username: Yup.string().required("Username is required"),
  vehicleType: Yup.string().required("Vehicle type is required"),
  baseFarePrice: Yup.string().required("Base fare price is required"),
  baseFareDistance: Yup.string().required("Base fare distance is required"),
  email: Yup.string()
    .email("Invalid email format")
    .required("Email is required"),
  password: Yup.string()
    .required("Password is required")
    .min(6, "Password must be at least 6 characters"),
});

async function onSubmit(values) {
  const usersStore = useUsersStore();
  const alertStore = useAlertStore();
  try {
    await usersStore.register(values);
    await router.push("/account/login");
    alertStore.success("Registration successful");
  } catch (error) {
    alertStore.error(error);
  }
}
</script>
