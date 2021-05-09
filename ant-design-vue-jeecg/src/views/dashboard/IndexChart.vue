<template>
  <div class="page-header-index-wide">
    <a-row>
      <a-carousel arrows autoplay="true">
        <div
          slot="prevArrow"
          slot-scope="props"
          class="custom-slick-arrow"
          style="left: 10px;zIndex: 1"
        >
          <a-icon type="left-circle" />
        </div>
        <div slot="nextArrow" slot-scope="props" class="custom-slick-arrow" style="right: 10px">
          <a-icon type="right-circle" />
        </div>

        <div align="center">
          <img src="~@/assets/4.jpg"  width="1200" align="center" height="160">
        </div>
        <div align="center">
          <img src="~@/assets/2.jpg"  width="1200" align="center" height="160">
        </div>
        <div align="center">
          <img src="~@/assets/1.jpg"  width="1200" align="center" height="160">
        </div>
      </a-carousel>
    </a-row>
<!--
    <span>
      <img src="~@/assets/indexImg.jpg" :style="{ marginBottom: '24px' }"  width="550" align="right" height="420">
    </span>
-->


    <!--<a-row :gutter="24">-->
      <!--<a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }" :align="center">-->
        <!--<chart-card :loading="loading" title="支付笔数" :total="6560 | NumberFormat">-->
          <!--<a-tooltip title="指标说明" slot="action">-->
            <!--<a-icon type="info-circle-o" />-->
          <!--</a-tooltip>-->
          <!--<div>-->
            <!--<mini-bar :height="40" />-->
          <!--</div>-->
          <!--<template slot="footer">转化率 <span>60%</span></template>-->
        <!--</chart-card>-->
      <!--</a-col>-->
      <!--<a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }" :align="center">-->
        <!--<chart-card :loading="loading" title="运营活动效果" total="78%">-->
          <!--<a-tooltip title="指标说明" slot="action">-->
            <!--<a-icon type="info-circle-o" />-->
          <!--</a-tooltip>-->
          <!--<div>-->
            <!--<mini-progress color="rgb(19, 194, 194)" :target="80" :percentage="78" :height="8" />-->
          <!--</div>-->
          <!--<template slot="footer">-->
            <!--<trend flag="down" style="margin-right: 16px;">-->
              <!--<span slot="term">同周比</span>-->
              <!--12%-->
            <!--</trend>-->
            <!--<trend flag="up">-->
              <!--<span slot="term">日环比</span>-->
              <!--80%-->
            <!--</trend>-->
          <!--</template>-->
        <!--</chart-card>-->
      <!--</a-col>-->
    <!--</a-row>-->
    <a-row :gutter="24">
      <a-col :sm="24" :md="12" :xl="6" :style="{ marginBottom: '24px' }" >
      </a-col>
    </a-row>
    <div>

    <a-card :bordered="false">
      <a-row>
        <a-col :sm="8" :xs="24">
          <head-info title="成交数" :content="payNum " :bordered="true"/>
        </a-col>
        <a-col :sm="8" :xs="24">
          <head-info title="成交量" :content="payWeight" :bordered="true"/>
        </a-col>
        <a-col :sm="8" :xs="24">
          <head-info title="销售金额" :content="payMoney"/>
        </a-col>
      </a-row>
    </a-card>

    </div>


<!--
    <a-card :loading="loading" :bordered="false" :body-style="{padding: '0'}">
      <div class="salesCard">
        <a-tabs default-active-key="1" size="large" :tab-bar-style="{marginBottom: '24px', paddingLeft: '16px'}">
          <div class="extra-wrapper" slot="tabBarExtraContent">
            <div class="extra-item">
              <a>今日</a>
              <a>本周</a>
              <a>本月</a>
              <a>本年</a>
            </div>
            <a-range-picker :style="{width: '256px'}" />
          </div>
          <a-tab-pane loading="true" tab="销售额" key="1">
            <a-row>
              <a-col :xl="16" :lg="12" :md="12" :sm="24" :xs="24">
                <bar title="销售额排行" :dataSource="barData"/>
              </a-col>
              <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">
                <rank-list title="门店销售排行榜" :list="rankList"/>
              </a-col>
            </a-row>
          </a-tab-pane>
          <a-tab-pane tab="销售趋势" key="2">
            <a-row>
              <a-col :xl="16" :lg="12" :md="12" :sm="24" :xs="24">
                <bar title="销售额趋势" :dataSource="barData"/>
              </a-col>
              <a-col :xl="8" :lg="12" :md="12" :sm="24" :xs="24">
                <rank-list title="门店销售排行榜" :list="rankList"/>
              </a-col>
            </a-row>
          </a-tab-pane>
        </a-tabs>
      </div>
    </a-card>
-->

    <a-row >
      <a-col :span="24">
        <a-card :loading="loading" :bordered="false" title="最近一周访问量统计" :style="{ marginTop: '24px' }">
          <a-row>
            <a-col :span="6">
              <head-info title="今日IP" :content="loginfo.todayIp"></head-info>
            </a-col>
            <a-col :span="2">
              <a-spin class='circle-cust'>
                <a-icon slot="indicator" type="environment" style="font-size: 24px"  />
              </a-spin>
            </a-col>
            <a-col :span="6">
              <head-info title="今日访问" :content="loginfo.todayVisitCount"></head-info>
            </a-col>
            <a-col :span="2">
              <a-spin class='circle-cust'>
                <a-icon slot="indicator" type="team" style="font-size: 24px"  />
              </a-spin>
            </a-col>
            <a-col :span="6">
              <head-info title="总访问量" :content="loginfo.totalVisitCount"></head-info>
            </a-col>
            <a-col :span="2">
              <a-spin class='circle-cust'>
                <a-icon slot="indicator" type="rise" style="font-size: 24px"  />
              </a-spin>
            </a-col>
          </a-row>
          <line-chart-multid :fields="visitFields" :dataSource="visitInfo"></line-chart-multid>
        </a-card>
      </a-col>
    </a-row>
  </div>
</template>

<script>
  import ChartCard from '@/components/ChartCard'
  import ACol from "ant-design-vue/es/grid/Col"
  import ATooltip from "ant-design-vue/es/tooltip/Tooltip"
  import MiniArea from '@/components/chart/MiniArea'
  import MiniBar from '@/components/chart/MiniBar'
  import MiniProgress from '@/components/chart/MiniProgress'
  import RankList from '@/components/chart/RankList'
  import Bar from '@/components/chart/Bar'
  import LineChartMultid from '@/components/chart/LineChartMultid'
  import HeadInfo from '@/components/tools/HeadInfo.vue'

  import Trend from '@/components/Trend'
  import { getLoginfo,getVisitInfo } from '@/api/api'
  import { httpAction, getAction } from '@/api/manage'

  const rankList = []
  for (let i = 0; i < 7; i++) {
    rankList.push({
      name: '白鹭岛 ' + (i+1) + ' 号店',
      total: 1234.56 - i * 100
    })
  }
  const barData = []
  for (let i = 0; i < 12; i += 1) {
    barData.push({
      x: `${i + 1}月`,
      y: Math.floor(Math.random() * 1000) + 200
    })
  }
  export default {
    name: "IndexChart",
    components: {
      ATooltip,
      ACol,
      ChartCard,
      MiniArea,
      MiniBar,
      MiniProgress,
      RankList,
      Bar,
      Trend,
      LineChartMultid,
      HeadInfo
    },
    data() {
      return {
        loading: true,
        center: null,
        rankList,
        barData,
        payNum: '',
        payWeight: '',
        payMoney: '',
        loginfo:{},
        visitFields:['ip','visit'],
        visitInfo:[],
        indicator: <a-icon type="loading" style="font-size: 24px" spin />
      }
    },
    created() {
      setTimeout(() => {
        this.loading = !this.loading
      }, 1000)
      this.initLogInfo();
      this.saleInfo();
    },
    methods: {
      initLogInfo () {
        console.log("flag")
        getLoginfo(null).then((res)=>{
          if(res.success){
            Object.keys(res.result).forEach(key=>{
              res.result[key] =res.result[key]+""
            })
            this.loginfo = res.result;
          }
        })
        getVisitInfo().then(res=>{
          if(res.success){
            console.log("res",res)
             this.visitInfo = res.result;
           }
         })
      },
      saleInfo(){
          var that =this
          var params = {}
          getAction("/ord/orderBooking/saleInfo",params).then((res)=>{
            if(res.success){
              that.payNum = res.result.num + ' '+ '单';
              that.payWeight =res.result.weight + ' ' + '吨/t'
              that.payMoney = res.result.money + ' ' + '元'
              console.log("saleInfo",res)
            }else{

            }
          });
      },
    }
  }
</script>

<style  scoped>


  .circle-cust{
    position: relative;
    top: 28px;
    left: -100%;
  }
  .extra-wrapper {
    line-height: 55px;
    padding-right: 24px;

    .extra-item {
      display: inline-block;
      margin-right: 24px;

      a {
        margin-left: 24px;
      }
    }
  }

  /* 首页访问量统计 */
  .head-info {
    position: relative;
    text-align: left;
    padding: 0 32px 0 0;
    min-width: 125px;

    &.center {
      text-align: center;
      padding: 0 32px;
    }

    span {
      color: rgba(0, 0, 0, .45);
      display: inline-block;
      font-size: .95rem;
      line-height: 42px;
      margin-bottom: 4px;
    }
    p {
      line-height: 42px;
      margin: 0;
      a {
        font-weight: 600;
        font-size: 1rem;
      }
    }
  }

  .ant-carousel >>> .slick-slide {
    text-align: center;
    height: 160px;
    line-height: 160px;
    background: #4A708B;
    overflow: hidden;
  }

  .ant-carousel >>> .custom-slick-arrow {
    width: 25px;
    height: 25px;
    font-size: 25px;
    color: #fff;
    background-color: rgba(31, 45, 61, 0.11);
    opacity: 0.3;
  }
  .ant-carousel >>> .custom-slick-arrow:before {
    display: none;
  }
  .ant-carousel >>> .custom-slick-arrow:hover {
    opacity: 0.5;
  }

  .ant-carousel >>> .slick-slide h3 {
    color: #fff;
  }

</style>