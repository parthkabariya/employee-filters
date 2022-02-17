<template>
  <div class="font-normal">
    <filters 
      :dataFilters="dataFilters"
      :dataColumns="dataColumns"
      :dataTypes="dataTypes"
      @changeDataFilters="changeDataFilters"
    />
    <div class="border-collapse overflow-auto" style="height:calc(100vh - 300px)" v-if="employees.length > 0">
      <div class="mx-2 font-bold"><span class="text-green-500">Total Records: </span> <span class="text-blue-500">{{employees.length}}</span></div>
    <table class="table-auto" >
      <tr>
        <template v-for="(col, index) of dataColumns">
          <th v-if="col != 'id'" :key="index" class="px-1 border border-blue-500 text-center capitalize">
            {{col}}
          </th>
        </template>
      </tr>
      <tr v-for="(emp) of employees" :key="emp.id">
        <template v-for="(col, index) of dataColumns">
          <td v-if="col != 'id'" :key="index" class="border border-blue-300 text-center">
            {{emp[col]}}
          </td>
        </template>
      </tr>
    </table>
    </div>
    <div v-else class="text-center font-bold text-5xl">No Data Available</div>
  </div>
</template>

<script>
import data from '../static/borrowers.json'
import Filters from './Filters.vue';
export default {
  components: { Filters },
  name: "CustomTable",
  data() {
    return {
      employees: data,
      dataFilters: [
        {
          'column': null,
          'operator': null,
          'value': null
        }
      ],
    }
  },
  computed: {
    dataColumns: function () {
      return Object.keys(data[0])
    },
    dataTypes: function () {
      let newDataTypes = {};
      for(let key of Object.keys(data[0])){
        if(key == 'dateOfBirth' || key == 'startDate' ) {
          newDataTypes[key] = 'dates'
        } else if (key == 'creditScore' || key == 'w2Income') {
          newDataTypes[key] = 'numeric'
        } else {
          newDataTypes[key] = 'string'
        }
      }
      return newDataTypes;
    }
  },
  methods: {
    filterEmployees(modifiedFilters, filterCondition) {
      let filteredEmployees = data;
      let filteredEmployeesArray = [];

      for(let filterValue of Object.values(modifiedFilters)){
        if(filterCondition == 'OR') {
          filteredEmployees = data.filter((obj) => {
            if(filterValue.column && filterValue.operator && filterValue.value){
              if(this.selectOperator(filterValue, obj))
                return obj;
            } else {
              return obj;
            }
          });
          filteredEmployeesArray = [...filteredEmployeesArray, ...filteredEmployees];

        } else {
          filteredEmployees = filteredEmployees.filter((obj) => {
            if(filterValue.column && filterValue.operator && filterValue.value){
              if(this.selectOperator(filterValue, obj))
                return obj;
            } else {
              return obj;
            }
          });
        }
        
      }
      if(filterCondition == 'OR') {
        filteredEmployees = filteredEmployeesArray;
      }
      this.employees = filteredEmployees;
    },
    changeDataFilters({newDataFilters, filterCondition}) {
      this.dataFilters = newDataFilters;
      this.filterEmployees(newDataFilters, filterCondition);
    },
    selectOperator(filterValue, obj) {
      switch(filterValue.operator) {
        case 'Equals':
          if(obj[filterValue.column].toLowerCase() == filterValue.value.toLowerCase()) {
            return true;
          }
          return false
        case 'Includes':
          if(obj[filterValue.column].toLowerCase().includes(filterValue.value.toLowerCase())) {
            return true;
          }
          return false;
        case 'Greater than':
          if(this.dataTypes[filterValue.column] == 'dates') {
            if(new Date(obj[filterValue.column]).getTime() > new Date(filterValue.value).getTime()) {
              return true;
            }
          } else {
            if(obj[filterValue.column] > filterValue.value) {
              return true;
            }
          } 
          return false;
        case 'Less than':
          if(this.dataTypes[filterValue.column] == 'dates') {
            if(new Date(obj[filterValue.column]).getTime() < new Date(filterValue.value).getTime()) {
              return true;
            }
          } else {
            if(obj[filterValue.column] < filterValue.value) {
              return true;
            }
          } 
          return false;
        default:
          return false;
      }
    },
  }
};
</script>
