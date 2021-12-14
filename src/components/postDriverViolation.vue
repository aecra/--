<template>
  <div class="head">
    <span class="title">车队</span>
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
  <div class="head">
    <span class="title">线路</span>
    <el-select v-model="vline" placeholder="Select">
      <el-option
        v-for="item in data.line"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">站点</span>
    <el-select v-model="vstation" placeholder="Select">
      <el-option
        v-for="item in data.station"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">车辆</span>
    <el-select v-model="vbus" placeholder="Select">
      <el-option
        v-for="item in data.bus"
        :key="item.id.Int32"
        :label="item.plate.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">司机</span>
    <el-select v-model="vliablePerson" placeholder="Select">
      <el-option
        v-for="item in data.liablePerson"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">违章类型</span>
    <el-select v-model="vviolation" placeholder="Select">
      <el-option
        v-for="item in data.violation"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">违章时间</span>
    <el-date-picker
      v-model="vviolationTime"
      type="datetime"
      placeholder="Select date and time"
    >
    </el-date-picker>
  </div>
  <div class="head">
    <span class="title">录入人</span>
    <el-select v-model="vinputBy" placeholder="Select">
      <el-option
        v-for="item in data.staff"
        :key="item.id.Int32"
        :label="item.name.String"
        :value="item.id.Int32"
      >
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <el-popconfirm
      confirm-button-text="是"
      cancel-button-text="否"
      title="是否提交信息?"
      @confirm="confirmEvent"
      @cancel="cancelEvent"
    >
      <template #reference>
        <el-button>提交信息</el-button>
      </template>
    </el-popconfirm>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from "vue";
import axios from "axios";
import { ElMessage } from "element-plus";

export default {
  name: "postDriverViolation",
  methods: {
    confirmEvent() {
      const that = this;
      axios
        .post("http://127.0.0.1:7814/DriverViolation", {
          liablePerson: that.vliablePerson,
          bus: that.vbus,
          station: that.vstation,
          violation: that.vviolation,
          violationTime: that.vviolationTime,
          inputBy: that.vinputBy,
          inputTime: new Date(),
        })
        .then((response) => {
          if (response.data.error === "") {
            ElMessage({
              showClose: true,
              message: "录入成功.",
              type: "success",
            });
          }
        });
    },
    cancelEvent() {
      console.log("cancel");
    },
  },
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
        .get("http://127.0.0.1:7814/AStation?line=" + id)
        .then(function (response) {
          that.data.station = response.data;
        });
      axios
        .get("http://127.0.0.1:7814/Driver?line=" + id)
        .then(function (response) {
          that.data.liablePerson = response.data;
        });
      axios
        .get("http://127.0.0.1:7814/ABus?line=" + id)
        .then(function (response) {
          that.data.bus = response.data;
        });
    },
  },
  setup() {
    const data = reactive({
      fleet: [],
      line: [],
      station: [],
      bus: [],
      liablePerson: [],
      violation: [],
      staff: [],
    });
    const getUserRepositories = () => {
      axios.get("http://127.0.0.1:7814/AFleet").then(function (response) {
        data.fleet = response.data;
      });
      axios
        .get("http://127.0.0.1:7814/AViolationKind")
        .then(function (response) {
          data.violation = response.data;
        });
      axios.get("http://127.0.0.1:7814/AStaff").then(function (response) {
        data.staff = response.data;
      });
    };

    onMounted(getUserRepositories);

    return {
      data,
      vfleet: ref(""),
      vline: ref(""),
      vliablePerson: ref(""),
      vbus: ref(""),
      vstation: ref(""),
      vviolation: ref(""),
      vnativePlace: ref(""),
      vviolationTime: ref(new Date()),
      vinputBy: ref(""),
    };
  },
};
</script>

<style scoped>
.head {
  margin: 20px 0px;
}
.title {
  display: inline-block;
  width: 100px;
  margin-right: 20px;
}
</style>
