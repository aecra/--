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
    <span class="title">请选择查询的司机</span>
    <el-select v-model="vfleet" placeholder="选择" style="margin-right:10px">
      <el-option
        v-for="item in data.fleet"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
    <el-select v-model="vline" placeholder="选择" style="margin-right:10px">
      <el-option
        v-for="item in data.line"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
    <el-select v-model="vdriver" placeholder="选择" style="margin-right:10px">
      <el-option
        v-for="item in data.driver"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">请选择查询司机的员工编号</span>
    <el-input v-model="driverNumber" placeholder="请输入员工编号" style="width:auto" />
  </div>
  <div>
    <el-table :data="data.violation" stripe style="width: 100%">
      <el-table-column prop="number.String" label="员工编号" />
      <el-table-column prop="name.String" label="姓名" />
      <el-table-column prop="fleet.String" label="车队" />
      <el-table-column prop="line.String" label="线路" />
      <el-table-column prop="station.String" label="站点" />
      <el-table-column prop="busPlate.String" label="车辆" />
      <el-table-column prop="violation.String" label="违章类型" />
      <el-table-column prop="violationTime.Time" label="违章时间" />
      <el-table-column prop="inputBy.String" label="录入人" />
      <el-table-column prop="inputTime.Time" label="录入时间" />
    </el-table>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from "vue";
import axios from "axios";

export default {
  name: "getDriverViolation",
  methods: {},
  watch: {
    vfleet(id) {
      const that = this;
      axios
        .get("http://127.0.0.1:7814/ALine?fleet=" + id)
        .then(function (response) {
          that.data.line = response.data;
        });
    },
    vline(id) {
      const that = this;
      axios
        .get("http://127.0.0.1:7814/Driver?fleet=" + id)
        .then(function (response) {
          that.data.driver = response.data;
        });
    },
    vdriver(id) {
      const that = this;
      axios
        .get(`http://127.0.0.1:7814/DriverViolation?driver=${id}&start=${that.time[0].toISOString()}&end=${that.time[1].toISOString()}`)
        .then(function (response) {
          if (response.data)
            response.data.forEach((element) => {
              element.violationTime.Time = new Date(
                element.violationTime.Time
              ).toLocaleDateString();
              element.inputTime.Time = new Date(
                element.inputTime.Time
              ).toLocaleDateString();
            });
          that.data.violation = response.data;
        });
    },
    driverNumber(number) {
      const that = this;
      axios
        .get(
          `http://127.0.0.1:7814/DriverViolation?number=${number}&start=${that.time[0].toISOString()}&end=${that.time[1].toISOString()}`)
        .then(function (response) {
          if (response.data)
            response.data.forEach((element) => {
              element.violationTime.Time = new Date(
                element.violationTime.Time
              ).toLocaleDateString();
              element.inputTime.Time = new Date(
                element.inputTime.Time
              ).toLocaleDateString();
            });
          that.data.violation = response.data;
        });
    },
  },
  setup() {
    const data = reactive({ fleet: [], line: [], driver: [], violation: [] });
    const getUserRepositories = () => {
      axios.get("http://127.0.0.1:7814/AFleet").then(function (response) {
        data.fleet = response.data;
      });
    };

    onMounted(getUserRepositories);

    return {
      vfleet: ref(""),
      vline: ref(""),
      vdriver: ref(""),
      driverNumber: ref(""),
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
