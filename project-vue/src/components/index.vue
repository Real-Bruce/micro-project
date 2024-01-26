<template>
  <div class="navbar" style="display: flex; align-items: center; justify-content: center">
    <img width="50" alt="logo" src="@/assets/logo.png">
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
                :autoplay="false"
                interval="400"
                trigger="click"
                indicator-position="none"
                @change="leftChangePage"
                :initial-index="0"
                :loop="false"
                ref="carouselRef"
            >
              <el-carousel-item v-for="item in listManual" :key="item">
                <h3 text="2xl" justify="center">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
        </el-col>
        <el-col :span="4" align="middle">
          <div class="btn-group">
            <el-button type="success" v-if="!isAutoplay" :disabled="listAuto.length <= 1" @click="clickChange">开始匹配
            </el-button>
            <el-button type="danger" v-if="isAutoplay" @click="clickChange">停止匹配</el-button>
<!--            <el-button type="warning" :disabled="true" @click="matchChange">分组切换</el-button>-->
            <el-button type="primary" :disabled="isAutoplay && listAuto.length <= 0" @click="groupList">分组</el-button>
            <el-button type="warning" @click="dialogVisible = true">分组结果</el-button>
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
            >
              <el-carousel-item v-for="item in listAuto" :key="item">
                <h3 text="2xl" justify="center">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
        </el-col>
      </el-row>
    </div>

    <div class="c-title">
      <!-- 此处为结果集合-->
      <div class="t-list" style="display: flex; flex-wrap: wrap;">
        <div class="ml-4 r-group" v-for="item in listCombo.slice(0,4)" :key=item>
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
</template>

<script>
export default {
  inject:['reload'],  //注入依赖
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
      isLoop: true
      ,
      listCombo: []
      ,
      itemManual: ""
      ,
      itemAuto: ""
      ,
      dialogVisible: false
    }
  },
  methods: {
    // 重置
    clearGroupList() {
      this.listCombo = []
      this.itemManual = this.listManual[0]
      this.itemAuto = this.listAuto[0]
      this.startIndex = 0
      this.reload();
    },
    // 构成分组
    groupList: function () {
      console.log("构成分组")
      let data = {
        itemManual: this.itemManual,
        itemAuto: this.itemAuto
      }
      // 构成分组
      this.listCombo.push(data)
      console.log("构成数组：", data)
      // 操作自动组，删除匹配组
      this.listAuto = this.listAuto.filter(item => {
        return item !== this.itemAuto
      });
      // 设置随机位置
      this.startIndex = this.getRandomArbitrary(0, this.listAuto.length);

      // 操作手动组
      this.$refs.carouselRef.next
      /*this.listManual = this.listManual.filter(item => {
        return item !== this.itemManual
      });*/
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
      console.log("----------------")
      console.log("手动切换当前值：", data)
      console.log("listA的值：",)
      this.itemManual = this.listManual[data]
    },
    // 右轮盘
    changePage(data) {
      console.log("----------------")
      console.log("自动切换当前值：", data)
      console.log("listB的值：", this.listManual[data])
      this.itemAuto = this.listAuto[data]
    },
    getRandomArbitrary(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    }
  },
  mounted() {
    this.itemManual = this.listManual[0]
    this.itemAuto = this.listAuto[0]
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
//padding-bottom: 100px; height: calc(100vh - 0px); //padding-top: 20px; padding-left: 20px; padding-right: 20px;

  .c-title {
    padding-top: 80px;

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
