<template>
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
    <el-table :data="data.driver" stripe style="width: 100%">
      <el-table-column prop="line.String" label="线路" />
      <el-table-column prop="number.String" label="员工编号" />
      <el-table-column prop="name.String" label="姓名" />
      <el-table-column prop="sex.String" label="性别" />
      <el-table-column prop="nativePlace.String" label="籍贯" />
      <el-table-column prop="idNumber.String" label="身份证号" width="180" />
      <el-table-column prop="phone.String" label="手机号" width="120" />
      <el-table-column prop="position.String" label="职位" />
      <el-table-column prop="wages.Float64" label="工资" />
      <el-table-column prop="office.String" label="办公室" />
      <el-table-column prop="entryTime.Time" label="入职时间" />
      <el-table-column prop="departureTime.Time" label="离职时间" />
    </el-table>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from "vue";
import axios from "axios";

export default {
  name: "getDriver",
  methods: {},
  watch: {
    vfleet(id) {
      const that = this;
      axios
        .get("http://127.0.0.1:7814/Driver?fleet=" + id)
        .then(function (response) {
          response.data.forEach((element) => {
            element.entryTime.Time = new Date(
              element.entryTime.Time
            ).toLocaleDateString();

            if (element.departureTime.Time === "0001-01-01T00:00:00Z")
              element.departureTime.Time = "";
            else
              element.departureTime.Time = new Date(
                element.departureTime.Time
              ).toLocaleDateString();
          });
          that.data.driver = response.data;
        });
    },
  },
  setup() {
    const data = reactive({ fleet: [], driver: [] });
    const getUserRepositories = () => {
      axios.get("http://127.0.0.1:7814/AFleet").then(function (response) {
        data.fleet = response.data;
      });
    };

    onMounted(getUserRepositories);

    return {
      vfleet: ref(""),
      data,
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
