<script setup>
import { storeToRefs } from "pinia";
import { ref } from "vue";
import { useUsersStore } from "@/stores";

import Papa from "papaparse";
const usersStore = useUsersStore();
const { users } = storeToRefs(usersStore);
const file = ref("");
const content = ref([]);
const parsed = ref(false);
const handleFileUpload = (event) => {
  const file = event.target.files[0];
  parseFile(file);
};
const parseFile = (file) => {
  Papa.parse(file, {
    header: true,
    skipEmptyLines: true,
    complete: (results) => {
      // results.data contains the parsed CSV data
      content.value = results.data;
      parsed.value = true;
      updateFares();
    },
  });
};
const clearFile = () => {};
async function updateFares() {
  users.value.forEach(async (user) => {
    // const matchingContent = content.value.find(
    //   (c) => c.username === user.username
    // );
    console.log(content.value[0].distanceTravel);
    if (content.value) {
      if (
        Number(content.value[0].distanceTravel) > Number(user.baseFareDistance)
      ) {
        let temp =
          Number(user.baseFarePrice) +
          (Number(content.value[0].costPerDistanceTraveled) *
            (Number(content.value[0].distanceTravel) -
              Number(user.baseFareDistance))) /
            Number(content.value[0].traveledUnit);
        user.fare = temp.toString();
      } else {
        user.fare = user.baseFarePrice;
      }
      let message;
      await usersStore.update(user.id, user);
      message = "User updated";
    }
  });
}
usersStore.getAll();
</script>

<template>
  <div>
    <h1>Users</h1>
    <router-link to="/users/add" class="btn btn-sm btn-success mb-2"
      >Add User</router-link
    >

    <div>
      <!-- <button @click="clearFile">Xóa File</button> -->
      <input type="file" accept=".csv" @change="handleFileUpload($event)" />
    </div>

    <table class="table table-striped">
      <thead>
        <tr>
          <th style="width: 15%">First Name</th>
          <th style="width: 15%">Last Name</th>
          <th style="width: 15%">Username</th>
          <td style="width: 15%">Email</td>
          <td style="width: 15%">Vehicle Type</td>
          <td style="width: 15%">Base Fare Distance</td>
          <td style="width: 15%">Base Fare Price</td>
          <td style="width: 15%">Fare</td>
          <th style="width: 5%"></th>
        </tr>
      </thead>
      <tbody>
        <template v-if="users.length">
          <tr v-for="user in users" :key="user.id">
            <td>{{ user.firstName }}</td>
            <td>{{ user.lastName }}</td>
            <td>{{ user.username }}</td>
            <td>{{ user.email }}</td>
            <td>{{ user.vehicleType }}</td>
            <td>{{ user.baseFareDistance }}</td>
            <td>{{ user.baseFarePrice }}</td>
            <td>{{ user.fare }}</td>
            <td style="white-space: nowrap">
              <router-link
                :to="`/users/edit/${user.id}`"
                class="btn btn-sm btn-primary mr-1"
                >Edit</router-link
              >
              <button
                @click="usersStore.delete(user.id)"
                class="btn btn-sm btn-danger btn-delete-user"
                :disabled="user.isDeleting"
              >
                <span
                  v-if="user.isDeleting"
                  class="spinner-border spinner-border-sm"
                ></span>
                <span v-else>Delete</span>
              </button>
            </td>
          </tr>
        </template>
        <tr v-if="users.loading">
          <td colspan="4" class="text-center">
            <span class="spinner-border spinner-border-lg align-center"></span>
          </td>
        </tr>
        <tr v-if="users.error">
          <td colspan="4">
            <div class="text-danger">
              Error loading users: {{ users.error }}
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>
