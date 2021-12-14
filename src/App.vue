<template>
<el-main>
  <el-tabs type="card">
    <el-tab-pane label="车队下司机信息查询">
      <getDriver></getDriver>
    </el-tab-pane>
    <el-tab-pane label="司机违章">
      <getDriverViolation></getDriverViolation>
    </el-tab-pane>
    <el-tab-pane label="车队违章">
      <getFleetViolation></getFleetViolation>
    </el-tab-pane>
    <el-tab-pane label="司机信息录入">
      <postDriver></postDriver>
    </el-tab-pane>
    <el-tab-pane label="车辆信息录入">
      <postBus></postBus>
    </el-tab-pane>
    <el-tab-pane label="违章信息录入">
      <postDriverViolation></postDriverViolation>
    </el-tab-pane>
  </el-tabs>
</el-main>
</template>

<script>
import getDriver from "./components/getDriver.vue";
import getDriverViolation from "./components/getDriverViolation.vue";
import getFleetViolation from "./components/getFleetViolation.vue";
import postDriver from "./components/postDriver.vue";
import postBus from "./components/postBus.vue"
import postDriverViolation from "./components/postDriverViolation.vue"

export default {
  name: "App",
  components: {
    getDriver,
    getDriverViolation,
    getFleetViolation,
    postDriver,
    postBus,
    postDriverViolation,
  },
};
</script>

<style>
</style>
