<template>
  <div class="grid justify-center gap-10 pt-20 pb-20 ">
    <div class="grid grid-cols-1 md:grid-cols-5 gap-10">
      <div class="font-bold">Filters</div>
      <button class="py-1 px-2 bg-blue-500 text-white rounded-md" @click="addNewFilter">Add Filters</button>
      <select class="rounded-sm px-2" name="condition" v-model="condition">
          <option>AND</option>
          <option>OR</option>
      </select>
    </div>
    <div class="grid grid-cols-1 md:grid-cols-5 gap-10" v-for="(filter, index) in currentFilter" :key="index" >
      <span class="font-bold">Where</span>
      <select class="rounded-sm" name="column" v-model="currentFilter[index]['column']" @change="resetOthers(index)">
        <template v-for="(col, index) of dataColumns">
          <option v-if="col != 'id'" :key="index">
            {{ col }}
          </option>
        </template>
      </select>
      <select class="rounded-sm" name="operationType" v-model="currentFilter[index]['operator']">
        <option v-show="dataTypes[currentFilter[index]['column']] == 'string'">
          Equals
        </option>
        <option v-show="dataTypes[currentFilter[index]['column']] == 'string'">
          Includes
        </option>
        <option v-show="dataTypes[currentFilter[index]['column']] == 'numeric' || dataTypes[currentFilter[index]['column']] == 'dates'">
          Greater than
        </option>
        <option v-show="dataTypes[currentFilter[index]['column']] == 'numeric' || dataTypes[currentFilter[index]['column']] == 'dates'">
          Less than
        </option>
      </select>
      <input class="rounded-sm px-2" v-model="currentFilter[index]['value']" placeholder="Search Value" />
      <button class="text-red-500 rounded-md" v-if="index != 0" @click="deleteFilter(index)">Delete</button>
    </div>
  </div>
</template>

<script>
// import data from '../static/borrowers.json'
export default {
  name: "CustomTable",
  data() {
    return {
      currentFilter: null,
      operators: [],
      condition: 'AND'
    };
  },
  props: ["dataFilters", "dataColumns", "dataTypes"],
  watch: {
    currentFilter: {
      handler: function (newFilters) {
        const data = {
            'newDataFilters': newFilters, 
            'filterCondition': this.condition
        }
        this.$emit("changeDataFilters", data);
      },
      deep: true,
    },
    condition: function (newValue) {
        const data = {
            'newDataFilters': this.currentFilter, 
            'filterCondition': newValue
        }
        this.$emit("changeDataFilters", data);
    }
  },
  created() {
    this.currentFilter = this.dataFilters;
  },
  methods: {
    addNewFilter() {
      this.currentFilter.push({
        column: null,
        operator: null,
        value: null,
      });
    },
    deleteFilter(index) {
        this.currentFilter.splice(index, 1);
    },
    resetOthers(index) {
        this.currentFilter[index]['operator'] = null
        this.currentFilter[index]['value'] = null
    }
  },
};
</script>
