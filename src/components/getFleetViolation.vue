<template>
<div class="head">
    <span class="title">选择查询的时间</span>
    <el-date-picker
      v-model="time"
      type="datetimerange"
      range-separator="To"
      start-placeholder="开始时间"
      end-placeholder="结束时间"
    >
    </el-date-picker>
  </div>
  <div class="head">
    <span class="title">请选择查询的车队</span>
    <el-select v-model="vfleet" placeholder="Select">
      <el-option
        v-for="item in data.fleet"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div>
    <el-table :data="data.violation" stripe style="width: 100%">
      <el-table-column prop="number.Int32" label="违章数量" />
      <el-table-column prop="violation.String" label="违章类型" />
    </el-table>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from "vue";
import axios from "axios";

export default {
  name: "getFleetViolation",
  methods: {},
  watch: {
    vfleet(id) {
      const that = this;
      axios
        .get(`http://127.0.0.1:7814/FleetViolation?fleet=${id}&start=${that.time[0].toISOString()}&end=${that.time[1].toISOString()}`)
        .then(function (response) {
          that.data.violation = response.data;
        });
        console.log(that.time[0].toISOString())
    },
  },
  setup() {
    const data = reactive({ fleet: [], violation: [] });
    const getUserRepositories = () => {
      axios.get("http://127.0.0.1:7814/AFleet").then(function (response) {
        data.fleet = response.data;
      });
    };

    onMounted(getUserRepositories);

    return {
      vfleet: ref(""),
      data,
      time: ref([new Date(2020, 10, 10, 10, 10), new Date()]),
    };
  },
};
</script>

<style scoped>
.head {
  margin: 20px 0px;
}
.title {
  margin-right: 20px;
}
</style>
