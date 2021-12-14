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
    <span class="title">型号</span>
    <el-input v-model="vmodel" placeholder="Please input" style="width: auto" />
  </div>
  <div class="head">
    <span class="title">颜色</span>
    <el-input v-model="vcolor" placeholder="Please input" style="width: auto" />
  </div>
  <div class="head">
    <span class="title">车牌号</span>
    <el-input v-model="vplate" placeholder="Please input" style="width: auto" />
  </div>
  <div class="head">
    <span class="title">核载人数</span>
    <el-input-number v-model="vpeople" :precision="0" />
  </div>
  <div class="head">
    <span class="title">购入时间</span>
    <el-date-picker
      v-model="vbuyTime"
      type="datetime"
      placeholder="Select date and time"
    >
    </el-date-picker>
  </div>
  <div class="head">
    <span class="title">购入人</span>
    <el-select v-model="vbuyBy" placeholder="Select">
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
  name: "postBus",
  methods: {
    confirmEvent() {
      const that = this;
      axios
        .post("http://127.0.0.1:7814/Bus", {
          model: that.vmodel,
          people: that.vpeople,
          color: that.vcolor,
          plate: that.vplate,
          lineId: that.vline,
          buyTime: that.vbuyTime,
          buyBy: that.vbuyBy,
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
    const data = reactive({ fleet: [], line: [], staff: [] });
    const getUserRepositories = () => {
      axios.get("http://127.0.0.1:7814/AFleet").then(function (response) {
        data.fleet = response.data;
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
      vmodel: ref(""),
      vpeople: ref(0),
      vcolor: ref(""),
      vplate: ref(""),
      vbuyTime: ref(new Date()),
      vbuyBy: ref(""),
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
