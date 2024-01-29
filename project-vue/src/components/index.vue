<template>
  <div class="navbar" style="display: flex; align-items: center; justify-content: center">
    <img width="120" alt="logo" src="@/assets/logo.png">
    <h1>{{ msg }}</h1>
  </div>
  <div class="container">
    <div class="c-body">
      <el-row justify="center">
        <el-col :span="10" align="middle">
          <div style=" width: 400px;">
            <el-carousel
                height="400px"
                direction="vertical"
                type="card"
                :autoplay="isAutoplay"
                interval="150"
                trigger="click"
                indicator-position="none"
                @change="leftChangePage"
                :initial-index="leftStartIndex"
                :loop="isLoop"
                ref="carouselLeftRef"
                :pause-on-hover="false"
            >
              <el-carousel-item v-for="item in listManual" :key="item">
                <h3 text="2xl" justify="center">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
        </el-col>
        <el-col :span="4" align="middle">
          <div class="btn-group">
            <el-switch
                :disabled="isLoading || isAutoplay"
                v-model="isAutoGroupList"
                class="mb-2"
                size="large"
                style="--el-switch-on-color: #13ce66; --el-switch-off-color: #409EFF"
                active-text="自动分组"
                inactive-text="手动分组"
                :active-action-icon="Cpu"
                :inactive-action-icon="User"
            />
            <div v-if="!isAutoGroupList">
              <el-button type="success" v-if="!isAutoplay" :disabled="listAuto.length <= 1" @click="clickChange">开始匹配
              </el-button>
              <el-button type="danger" :disabled="isLoading" v-if="isAutoplay" @click="clickChange">停止匹配</el-button>
              <el-button type="primary" :disabled="isAutoplay || listAuto.length <= 0 || listManual <= 0"
                         @click="groupList">分组
              </el-button>
            </div>
            <el-button v-if="isAutoGroupList" type="success"
                       :disabled="isLoading || listAuto.length <= 0 || listManual <= 0" @click="autoGroupList"
                       :loading="isLoading">
              自动分组
            </el-button>
            <!--            <el-button type="warning" :disabled="true" @click="matchChange">分组切换</el-button>-->
            <el-button type="warning" :disabled="isLoading || isAutoplay" @click="showDialog">分组结果</el-button>
            <el-button type="info" :disabled="isAutoplay" @click="clearGroupList">重置</el-button>
          </div>
        </el-col>
        <el-col :span="10" align="middle">
          <div style=" width: 400px;">
            <el-carousel
                height="400px"
                direction="vertical"
                type="card"
                :autoplay="isAutoplay"
                interval="150"
                trigger="click"
                indicator-position="none"
                @change="changePage"
                :initial-index="startIndex"
                :loop="isLoop"
                ref="carouselRightRef"
                :pause-on-hover="false"
            >
              <el-carousel-item v-for="item in listAuto" :key="item">
                <h3 text="2xl" justify="center">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
        </el-col>
      </el-row>
    </div>

    <div v-if="listCombo.length > 0" class="c-result">
      <!-- 此处为结果集合-->
      <div style="display: flex; flex-wrap: wrap; justify-content: center; align-items: center;">
        <h4>当前分组结果</h4>
      </div>
      <div class="t-list" style="display: flex; flex-wrap: wrap; justify-content: center; align-items: center;">
        <div class="ml-4 r-group" v-for="item in listCombo.slice(listCombo.length - 1, listCombo.length)" :key=item>
          <el-tag class="ml-2" type="success">{{ item.itemManual }}</el-tag>
          <el-tag class="ml-2" style="margin-left: 10px" type="danger">{{ item.itemAuto }}</el-tag>
        </div>
      </div>
    </div>

    <!-- 匹配结果集合 -->
    <el-dialog style="background-color: #2c3e50; color: #FFFFFF" v-model="dialogVisible" width="75%"
               center>
      <template #header="{ titleId }">
        <h4 :id="titleId">分组结果</h4>
      </template>
      <div class="cd-body" style="display: flex; flex-wrap: wrap;">
        <div class="ml-4 cdl-group" v-for="item in listCombo" :key=item>
          <el-tag class="ml-2" type="success">{{ item.itemManual }}</el-tag>
          <el-tag class="ml-2" style="margin-left: 10px" type="danger">{{ item.itemAuto }}</el-tag>
        </div>
      </div>
    </el-dialog>
  </div>

  <div class="c-bottom" style="display: flex; align-items: center; justify-content: center">
    <div>Copyright © {{ new Date().getFullYear() }} Bruce All rights reserved.</div>
  </div>
</template>

<script>
import {ElMessage, ElMessageBox} from 'element-plus'
import {Cpu, Hide, User, View} from "@element-plus/icons-vue";

export default {
  computed: {
    User() {
      return User
    },
    Cpu() {
      return Cpu
    },
    View() {
      return View
    },
    Hide() {
      return Hide
    }
  },
  inject: ['reload'],  //注入依赖
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data() {
    return {
      listManual: ['生产第一团支部', '生产第二团支部', '质量系统第一团支部', '财务管理团支部', '公司团工团—办公室联合团支部', '研发质量团支部', '小分子研发团支部', '大分子研发团支部', 'CMC管理团支部', '药学服务总公司总部第一团支部', '生物制品团支部', '商业部室第二团支部', '杏联健康团支部', '中西药销售中心第二团支部', '华东医药供应链管理（杭州）有限公司团支部', '江东职能团支部', '江东工程团支部']
      ,
      listAuto: ['生产第四团支部', '职能部室联合团支部', '人力资源—生活后勤联合团支部', '工程团支部', '工业微生物团支部', '创新研发团支部', '药学服务总公司总部第二团支部', '商业部室第一团支部', '中西药销售中心第一团支部', '中西药采购团支部', '医疗器械团支部', '零售管理团支部', '健康产业团支部', '药材参茸分公司团支部', '江东生产第一团支部', '江东生产第二团支部', '江东质量团支部']
      ,
      isAutoplay: false
      ,
      startIndex: 0
      ,
      leftStartIndex: 0
      ,
      // 是否循环显示
      isLoop: true
      ,
      listCombo: []
      ,
      itemManual: ""
      ,
      // 是否自动切换
      itemAuto: ""
      ,
      dialogVisible: false
      ,
      // 不重复随机数数组
      listRandom: new Set()
      ,
      // 是否加载中
      isLoading: false
      ,
      // 是否采用自动分组
      isAutoGroupList: false
      ,
      // 是否开启分组
      isGroupOption: false
    }
  },
  methods: {
    // 自动分组
    autoGroupList() {
      // 首次点击仅循环不分组
      if (this.isGroupOption) {
        this.groupList()
      }
      // 最后一次仅分组不循环
      if (this.listManual.length <= 1 || this.listAuto.length <= 1) {
        return;
      }

      this.clickChange()
      this.loadingChange()

      setTimeout(() => {
        this.loadingChange()
        this.clickChange()
        this.isGroupOption = true
      }, 3000)

    },
    // 自动分组-定时器实现
    autoGroupListOfTimeOut() {
      this.clickChange()
      this.loadingChange()

      setTimeout(() => {
        setTimeout(() => {
          this.groupList()
          this.loadingChange()
        }, 2000)
        this.clickChange()
      }, 3000)
    },
    loadingChange() {
      this.isLoading = !this.isLoading
    },
    // 展示弹出框
    showDialog() {
      if (this.listCombo <= 0) {
        ElMessage({
          message: '当前暂无分组信息!',
          type: 'warning',
        })
      } else {
        this.dialogVisible = true;
      }
    },
    // 重置
    clearGroupList() {
      ElMessageBox.confirm(
          '是否重置当前分组？',
          {
            confirmButtonText: 'OK',
            cancelButtonText: 'Cancel',
            type: 'warning',
          }
      ).then(() => {
        ElMessage({
          type: 'success',
          message: '重置分组成功',
        })
        this.initGroup()
        this.reload();
      }).catch(() => {
        ElMessage({
          type: 'info',
          message: '取消重置',
        })
      })
    },
    initGroup() {
      this.listCombo = []
      let randomA = this.getRandomArbitrary(0, this.listManual.length);
      let randomB = this.getRandomArbitrary(0, this.listAuto.length);
      this.leftStartIndex = randomA
      this.startIndex = randomB
      this.itemManual = this.listManual[randomA]
      this.itemAuto = this.listAuto[randomB]
    },
    // 构成分组
    groupList: function () {
      let data = {
        itemManual: this.itemManual,
        itemAuto: this.itemAuto
      }
      // 构成分组
      this.listCombo.push(data)
      // 操作自动组，删除匹配组
      this.listAuto = this.listAuto.filter(item => {
        return item !== this.itemAuto
      });
      // 设置随机位置
      this.startIndex = this.getRandomArbitrary(0, this.listAuto.length)
      this.$refs.carouselLeftRef.next();

      // 操作手动组
      this.listManual = this.listManual.filter(item => {
        return item !== this.itemManual
      });
      this.leftStartIndex = this.getRandomArbitrary(0, this.listAuto.length);
      this.$refs.carouselRightRef.next()
    },
    // 切换左右分组
    matchChange() {
      let tempA = this.listManual;
      let tempB = this.listAuto;
      this.listAuto = tempA;
      this.listManual = tempB
      this.clearGroupList();
    },
    clickChange() {
      this.isAutoplay = !this.isAutoplay
    },
    // 左边轮盘
    leftChangePage(data) {
      this.itemManual = this.listManual[data]
    },
    // 右轮盘
    changePage(data) {
      this.itemAuto = this.listAuto[data]
    },
    getRandomArbitrary(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    }
  },
  beforeMount() {
    this.initGroup()
  }
}
</script>

<style scoped>
.navbar {
  height: 70px;
  width: 100%;
  background: #1A1A1A;
  color: aliceblue;
  position: fixed;
  top: 0;
}

.container {
  background-color: #2c3e50;
  color: aliceblue;
  height: calc(100vh - 10px);
  padding-left: 20px;
  padding-right: 20px;

  .c-result {
    padding-top: 30px;

    .t-list {
      .r-group {
        height: 40px;
        margin-left: 10px;
        margin-top: 10px;
        background-color: azure;
        border: 2px;
        border-radius: 5px;
        display: flex;
        justify-content: center;
        align-items: center;
        padding: 0 10px;
      }
    }
  }

  .c-body {
    padding-top: 160px;

    .btn-group {
      display: flow;
      padding-top: 80px;

      .el-button {
        margin-left: 0px;
        margin-top: 20px;
        width: 80%;
      }
    }
  }


  .cd-body {
    display: flex;
    flex-wrap: wrap;

    .cdl-group {
      background-color: #2C3E50;
      padding: 10px;
      margin: 10px;
      border: 2px solid #475669;
      border-radius: 5px;
    }
  }
}

.c-bottom {
  position: fixed;
  background-color: #2c3e50;
  color: aliceblue;
  bottom: 0;
  width: 100%;
  padding-bottom: 10px;
}

.el-carousel__item h3 {
  color: #475669;
  opacity: 0.75;
  line-height: 200px;
  margin: 0;
  text-align: center;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
  border-radius: 5px;
}

.el-carousel__item:nth-child(2n + 1) {
  background-color: #d3dce6;
  border-radius: 5px;

}
</style>
