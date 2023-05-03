<template>
  <div>
    <button @click="fetchData()">Process</button>
    <div v-if="process">
      <div class="checkbox_Group">
        <div>
          <p>Traffic Flow</p>
          <hr />
          <p v-for="item in hdfValues" :key="item">
            <input
              type="checkbox"
              id="checkbox"
              v-model="checked"
              @change="checkboxChanged(item)"
            />
            <label :for="item">{{ getTimeString(item) }}</label>
          </p>
        </div>

        <div>
          <p>Incidents</p>
          <hr />

          <p v-for="item in hdtValues" :key="item">
            <input type="checkbox" id="checkbox" v-model="checked" />
            <label for="checkbox"> {{ getTimeString(item) }}</label>
          </p>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: "CheckBox",
  data() {
    return {
      Data: [],
      hdfData: [],
      hdfValues: [],
      hdtData: [],
      hdtValues: [],
      process: false,
    };
  },

  methods: {
    async fetchData() {
      try {
        this.process = true;
        const res = await axios.get(`http://localhost:3001/checkbox`);
        this.Data = res.data;

        //hdf
        const hdfArray = this.Data[0]?.filter((item) =>
          item.file_path.includes("HDF")
        );

        this.hdfData = hdfArray;
        const filePaths = hdfArray.map((item) => item.modified_at);
        this.hdfValues = filePaths;

        //hdt
        const hdtArray = this.Data[0]?.filter((item) =>
          item.file_path.includes("HDT")
        );

        this.hdtData = hdfArray;
        const filePath = hdtArray.map((item) => item.modified_at);
        this.hdtValues = filePath;
      } catch (e) {
        console.error(e);
      }
    },

    getTimeString(isoDateString) {
      const date = new Date(isoDateString);
      return date.toLocaleTimeString();
    },

    checkboxChanged(item) {
      console.log(
        `Checkbox for item ${item} changed. New value: ${this.checked}`
      );
      // Call your function here
    },
  },

  mounted() {},
};
</script>
<style>
.checkbox_Group {
  width: fit-content;
  border: 1px solid black;
  padding: 5px;
  border-radius: 5px;
  margin-top: 20px;
}
</style>
