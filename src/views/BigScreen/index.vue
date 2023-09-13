<template>
  <div class="all-charts">
    <div class="section-one">
      <img
        class="img-header"
        src="https://yjy-teach-oss.oss-cn-beijing.aliyuncs.com/smartPark/%E5%A4%A7%E5%B1%8F%E5%88%87%E5%9B%BE/%E5%9B%AD%E5%8C%BA%E6%A6%82%E5%86%B5%402x.png"
        alt="logo"
      />
      <div class="icons-container">
        <div class="item">
          <div class="icons-item building-icon">
            <span class="number"> {{ parkInfo?.base.buildingTotal }} </span>
          </div>
          <span class="title">楼宇总数</span>
          <span class="unity">（栋）</span>
        </div>
        <div class="item">
          <div class="icons-item enterprise-icon">
            <span class="number"> {{ parkInfo?.base.enterpriseTotal }} </span>
          </div>
          <span class="title">入驻企业总数</span>
          <span class="unity">（家）</span>
        </div>
        <div class="item">
          <div class="icons-item rod-icon">
            <span class="number"> {{ parkInfo?.base.parkingTotal }}</span>
          </div>
          <span class="title">车位总数</span>
          <span class="unity">（个）</span>
        </div>
        <div class="item">
          <div class="icons-item car-icon">
            <span class="number"> {{ parkInfo?.base.chargePoleTotal }} </span>
          </div>
          <span class="title">一体杆总数</span>
          <span class="unity">（个）</span>
        </div>
      </div>
    </div>
    <div class="section-two">
      <img
        class="img-header"
        src="https://yjy-teach-oss.oss-cn-beijing.aliyuncs.com/smartPark/%E5%A4%A7%E5%B1%8F%E5%88%87%E5%9B%BE/%E5%9B%AD%E5%8C%BA%E5%B9%B4%E5%BA%A6%E6%94%B6%E5%85%A5%E5%88%86%E6%9E%90%402x.png"
        alt="logo"
      />
      <div class="bar-chart-title">
        <span>单位：元</span>
        <div>
          <span class="bar-icon blue-bar-icon"></span>
          <span class="bar-icon red-bar-icon"></span>
          收入情况
        </div>
      </div>
      <div class="bar-chart" ref="barChart"></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { getParkInfo } from '@/api/park'
import { ref, onMounted } from 'vue'
import type { ParkResponseType } from '@/types/park'
// 引入Echarts
import * as echarts from 'echarts'
// 保存柱状图的dom节点
const barChart = ref(null)
// 园区数据
const parkInfo = ref<ParkResponseType>()
const initParkInfo = async () => {
  const parkRes = await getParkInfo()
  console.log('parkRes', parkRes)
  parkInfo.value = parkRes.data
}
// 初始化柱状图
const initBarChart = () => {
  const { parkIncome } = parkInfo.value!
  // 初始化指定容器
  const myBarChart = echarts.init(barChart.value)
  // 设置配置项
  const barOption = {
    grid: {
      top: '10px',
      left: '0px',
      right: '0px',
      bottom: '0px',
      containLabel: true
    },
    toolTip: {
      show: true,
      trigger: 'axis',
      axisPointer: {
        type: 'shadow'
      }
    },
    xAxis: {
      type: 'category',
      data: parkIncome.xMonth,
      axisTick: {
        show: false,
        alignWithLabel: true
      }
    },
    yAxis: {
      type: 'value',
      splitLine: false
    },
    textStyle: {
      color: '#b4c0cc'
    },
    series: [
      {
        data: parkIncome.yIncome.map((item, index) => {
          const color =
            index % 2 === 0
              ? {
                  type: 'linear',
                  x: 0,
                  y: 0,
                  x2: 0,
                  y2: 1,
                  colorStops: [
                    {
                      // 0%处的颜色
                      offset: 0,
                      color: '#74c0f8'
                    },
                    {
                      // 100%处的颜色
                      offset: 1,
                      color: 'rgba(116,192,248,0)'
                    }
                  ],
                  gloabl: false // 缺省为false
                }
              : {
                  type: 'linear',
                  x: 0,
                  y: 0,
                  x2: 0,
                  y2: 1,
                  colorStops: [
                    {
                      // 0%处的颜色
                      offset: 0,
                      color: '#ff7152'
                    },
                    {
                      // 100%处的颜色
                      offset: 1,
                      color: 'rgba(255,113,82,0)'
                    }
                  ],
                  gloabl: false // 缺省为false
                }
          return { value: item, itemStyle: { color } }
        }),
        type: 'bar',
        showBackground: false,
        barWidth: '10px',
        backgroundStyle: {
          color: 'rgba(180, 180, 180, 0.2)'
        }
      }
    ]
  }
  // 渲染柱状图
  barOption && myBarChart.setOption(barOption)
  /*  // 设置图表自适应
  window.addEventListener('resize', () => {
    myBarChart.resize()
  }) */
}

onMounted(async () => {
  await initParkInfo()
  initBarChart()
})
</script>

<style lang="scss" scoped>
.all-charts {
  position: absolute;
  left: 0;
  top: 0;
  width: 480px;
  height: 100vh;
  padding: 20px;
  display: flex;
  flex-direction: column;
  background: linear-gradient(
    to left,
    rgba(0, 6, 15, 0) 0%,
    rgba(0, 6, 15, 0) 20%,
    rgba(0, 0, 0, 0.4) 30%,
    rgba(0, 0, 0, 0.6) 40%,
    rgba(1, 4, 11, 1) 70%,
    #04070d 100%
  );

  .img-header {
    height: 30px;
  }

  .section-one {
    // flex-basis: 25%;
    .icons-container {
      display: flex;
      justify-content: space-between;
      padding: 20px 0;

      .item {
        display: flex;
        flex: 1;
        flex-direction: column;
        text-align: center;

        .icons-item {
          height: 80px;
          position: relative;

          .number {
            position: absolute;
            left: 50%;
            transform: translateX(-50%);
            font-size: 18px;
            color: #fff;
          }
        }

        .building-icon {
          background: url('@/assets/building-icon.png') no-repeat 50% 0 / contain;
        }
        .enterprise-icon {
          background: url('@/assets/enterprise-icon.png') no-repeat 50% 0 / contain;
        }
        .rod-icon {
          background: url('@/assets/rod-icon.png') no-repeat 50% 0 / contain;
        }
        .car-icon {
          background: url('@/assets/car-icon.png') no-repeat 50% 0 / contain;
        }

        .title {
          margin-top: 8px;
        }

        .title,
        .unity {
          font-size: 14px;
          color: #cdd7e1;
        }
      }
    }
  }
  .section-two {
    flex-basis: 35%;
    margin-top: 50px;
    .bar-chart-title {
      color: #c6d1db;
      display: flex;
      align-items: center;
      justify-content: space-between;
      font-size: 14px;
      margin-top: 20px;
      .bar-icon {
        display: inline-block;
        width: 12px;
        height: 4px;
      }
      .blue-bar-icon {
        background: linear-gradient(90deg, #74c0f8, rgba(116, 192, 248, 0));
      }
      .red-bar-icon {
        background: linear-gradient(90deg, #ff7152, rgba(255, 113, 82, 0));
      }
    }
    .bar-chart {
      width: 100%;
      height: calc(100% - 70px);
    }
  }
}
</style>
