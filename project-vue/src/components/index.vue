<template>
  <div class="navbar" style="display: flex; align-items: center; justify-content: center">
    <img width="50" alt="logo" src="@/assets/logo.png">
    <h1>{{ msg }}</h1>
  </div>
  <div class="container">
    <div class="c-title">
      <div class="t-list">
        <el-tag class="ml-2 l-tag" type="success" v-for="item in listA" :key=item>{{ item }}</el-tag>
      </div>
      <div class="t-list">
        <el-tag class="ml-2 l-tag" type="danger" v-for="item in listB" :key=item>{{ item }}</el-tag>
      </div>
    </div>

    <div class="c-body">
      <el-row>
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
                @change="changePage"
                :loop="false"
            >
              <el-carousel-item v-for="item in listA" :key="item">
                <h3 text="2xl" justify="center">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
        </el-col>
        <el-col :span="4" align="middle">
          <el-button type="primary" @click="clickChange">开始匹配</el-button>
          <!--          <el-button type="primary" @click="matchChange">切换匹配</el-button>-->
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
              <el-carousel-item v-for="item in listB" :key="item">
                <h3 text="2xl" justify="center">{{ item }}</h3>
              </el-carousel-item>
            </el-carousel>
          </div>
        </el-col>
      </el-row>


    </div>

  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String,
  },
  data() {
    return {
      listA: ['生产第一团支部', '生产第二团支部', '质量系统第一团支部', '财务管理团支部', '公司团工团—办公室联合团支部', '研发质量团支部', '小分子研发团支部', '大分子研发团支部', 'CMC管理团支部', '药学服务总公司总部第一团支部', '生物制品团支部', '商业部室第二团支部', '杏联健康团支部', '中西药销售中心第二团支部', '华东医药供应链管理（杭州）有限公司团支部', '江东职能团支部', '江东工程团支部']
      ,
      listB: ['生产第四团支部', '职能部室联合团支部', '人力资源—生活后勤联合团支部', '工程团支部', '工业微生物团支部', '创新研发团支部', '药学服务总公司总部第二团支部', '商业部室第一团支部', '中西药销售中心第一团支部', '中西药采购团支部', '医疗器械团支部', '零售管理团支部', '健康产业团支部', '药材参茸分公司团支部', '江东生产第一团支部', '江东生产第二团支部', '江东质量团支部']
      ,
      isAutoplay: false
      ,
      startIndex: 0
      ,
      isLoop: true

    }
  },
  methods: {
    matchChange() {
      this.isLoop = !this.isLoop
    }
    , clickChange() {
      this.isAutoplay = !this.isAutoplay
    }
    , changePage: function (data) {
      console.log("----------------")
      console.log("手动切换当前值：", data)
      console.log("listA的值：", this.listA[data])
      let randomNum = this.getRandomArbitrary(0, this.listA.length)
      console.log("随机值：", randomNum)
      console.log("listB的值：", this.listB[randomNum])
      console.log("分组信息：", this.listA[data], this.listB[randomNum])

      // this.startIndex = randomNum
    },
    getRandomArbitrary(min, max) {
      return Math.floor(Math.random() * (max - min)) + min;
    }
  },
  mounted() {

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
  height: calc(100vh - 0px);
//padding-top: 20px; padding-left: 20px; padding-right: 20px;

  .c-nav {
    height: 70px;
    width: 100%;
    background: #1A1A1A;
    position: fixed;
    top: 0;
  }

  .c-title {
    padding-top: 80px;

    .t-list {
      .l-tag {
        margin-top: 10px;
        margin-left: 10px;
      }
    }
  }

  .c-body {
    margin-top: 40px;
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
