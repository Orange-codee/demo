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
            type="submit"
            @click.prevent="saveData"
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
        <v-form>
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
                    :rules="nameRules"
                    required
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
        </v-form>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
console.log("3rd-20250227+");
const data = ref([]);
const getData = async () => {
  try {
    const res = await axios.get(
      "http://nexifytw.mynetgear.com:45000/api/Record/GetRecords"
    );
    const { Data = [] } = res.data; // 解構賦值，給 Data 設置默認值為空陣列
    if (!res?.data?.Success) throw new Error(res.data.Msg || "儲存失敗。");
    // data.value = res.data.Data;
    data.value = Data;
    console.log("getData中res:", res);
    console.log("data:", data);
  } catch (error) {
    return alert(error);
  }
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

const saveData = async () => {
  const isValid = data.value.every((item) => item.Name !== "");
  const nameSet = new Set(data.value.map((item) => item.Name));
  const isUnique = nameSet.size === data.value.length;

  try {
    //資料驗證
    if (!isValid) throw new Error("儲存失敗。請避免Name 為空值。");
    if (!isUnique) throw new Error("儲存失敗。請避免Name 為重複。");
    //API請求
    const res = await axios.post(
      "http://nexifytw.mynetgear.com:45000/api/Record/SaveRecords",
      data.value
    );
    if (!res?.data?.Success) throw new Error(res.data.Msg || "儲存失敗。");
    console.log("saveData:", res);
    console.log("newDataValue:", data);
    alert("儲存成功。");
    getData();
  } catch (error) {
    return alert(error);
  }
};

const nameRules = [
  (value) => {
    if (value) return true;

    return "請輸入姓名";
  },
];
</script>
