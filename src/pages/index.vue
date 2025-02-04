<template>
  <v-container>
    <v-row>
      <v-col>
        <div class="d-flex justify-space-between">
          <v-btn
            variant="outlined"
            color="teal"
            @click="addRow"
          >
            Add
          </v-btn><v-btn
            variant="outlined"
            color="teal"
            @click="saveData"
          >
            Save
          </v-btn>
          <v-btn
            variant="outlined"
            color="teal"
            @click="getData"
          >
            Update
          </v-btn>
        </div>
        <v-table>
          <thead>
            <tr>
              <th
                class="text-left"
                width="200"
              >
                <v-icon icon="mdi-account" />
                Name
              </th>
              <th
                class="text-left"
                width="300"
              >
                <v-icon icon="mdi-cake-variant" />
                Birthday
              </th>
              <th class="text-left">
                <v-icon icon="mdi-currency-usd" />
                Salary
              </th>
              <th
                class="text-left"
                width="200"
              >
                <v-icon icon="mdi-home" />
                Address
              </th>
            </tr>
          </thead>
          <tbody>
            <tr
              v-for="(item, key) in data"
              :key="'item' + key"
            >
              <td width="200">
                <v-text-field
                  v-model.trim="item.Name"
                  label="Name"
                  variant="outlined"
                  density="compact"
                  class="mt-5"
                />
              </td>
              <td width="300">
                <v-text-field
                  v-model="item.DateOfBirth"
                  :value="item.DateOfBirth.split('T')[0]"
                  label="Birthday"
                  type="date"
                  variant="outlined"
                  density="compact"
                  class="mt-5"
                />
              </td>
              <td>
                <v-slider
                  v-model="item.Salary"
                  color="teal-darken-4"
                  max="100000"
                  min="0"
                  class="mt-5"
                />
              </td>
              <td width="200">
                <v-text-field
                  v-model.trim="item.Address"
                  label="Address"
                  variant="outlined"
                  density="compact"
                  class="mt-5"
                />
              </td>
            </tr>
          </tbody>
        </v-table>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";

let data = ref([]);
const getData = () => {
  axios
    .get("http://nexifytw.mynetgear.com:45000/api/Record/GetRecords")
    .then((res) => {
      data.value = res.data.Data;
      console.log("data:", data);
    });
};

const addRow = () => {
  data.value.unshift({
    Name: "",
    DateOfBirth: new Date().toISOString().split("T")[0],
    Salary: 0,
    Address: "",
  });
  console.log("addRow", data);
};

const saveData = () => {
  axios
    .post(
      "http://nexifytw.mynetgear.com:45000/api/Record/SaveRecords",
      data.value
    )
    .then((res) => {
      console.log("saveData", res);
      console.log("newDataValue", data);
      getData();
    });
};
</script>
