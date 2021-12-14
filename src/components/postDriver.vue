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
    <span class="title">性别</span>
    <el-select v-model="vsex" placeholder="Select">
      <el-option v-for="item in sex" :key="item" :label="item" :value="item">
      </el-option>
    </el-select>
  </div>
  <div class="head">
    <span class="title">姓名</span>
    <el-input v-model="vname" placeholder="Please input" style="width: auto" />
  </div>
  <div class="head">
    <span class="title">籍贯</span>
    <el-input
      v-model="vnativePlace"
      placeholder="Please input"
      style="width: auto"
    />
  </div>
  <div class="head">
    <span class="title">身份证号</span>
    <el-input
      v-model="vidNumber"
      placeholder="Please input"
      style="width: auto"
    />
  </div>
  <div class="head">
    <span class="title">员工编号</span>
    <el-input
      v-model="vnumber"
      placeholder="Please input"
      style="width: auto"
    />
  </div>
  <div class="head">
    <span class="title">手机号</span>
    <el-input v-model="vphone" placeholder="Please input" style="width: auto" />
  </div>
  <div class="head">
    <span class="title">职位</span>
    <el-input
      v-model="vposition"
      placeholder="Please input"
      style="width: auto"
    />
  </div>
  <div class="head">
    <span class="title">工资</span>
    <el-input-number v-model="vwages" :precision="2" />
  </div>
  <div class="head">
    <span class="title">办公室</span>
    <el-input
      v-model="voffice"
      placeholder="Please input"
      style="width: auto"
    />
  </div>
  <div class="head">
    <span class="title">入职时间</span>
    <el-date-picker
      v-model="ventryTime"
      type="datetime"
      placeholder="Select date and time"
    >
    </el-date-picker>
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
var IDValidator = require("id-validator");
var GB2260 = require("id-validator/src/GB2260");

export default {
  name: "postDriver",
  methods: {
    confirmEvent() {
      var Validator = new IDValidator(GB2260);
      if (!Validator.isValid(this.vidNumber)) {
        ElMessage({ message: "身份证号错误", type: "warning" });
      }
      const that = this;
      axios
        .post("http://127.0.0.1:7814/Driver", {
          number: that.vnumber,
          name: that.vname,
          sex: that.vsex,
          nativePlace: that.vnativePlace,
          idNumber: that.vidNumber,
          phone: that.vphone,
          position: that.vposition,
          wages: that.vwages,
          office: that.voffice,
          entryTime: that.ventryTime,
          lineId: that.vline,
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
  },
  setup() {
    const data = reactive({ fleet: [], line: [] });
    const getUserRepositories = () => {
      axios.get("http://127.0.0.1:7814/AFleet").then(function (response) {
        data.fleet = response.data;
      });
    };

    onMounted(getUserRepositories);

    return {
      data,
      sex: ref(["男", "女"]),
      vfleet: ref(""),
      vline: ref(""),
      vsex: ref(""),
      vname: ref(""),
      vnativePlace: ref(""),
      vidNumber: ref(""),
      vnumber: ref(""),
      vphone: ref(""),
      vposition: ref(""),
      vwages: ref(0),
      voffice: ref(""),
      ventryTime: ref(new Date()),
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
