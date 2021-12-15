<template>
  <div id="myChart" style="width: 1500px; height: 600px;">

  </div>
</template>

<script>
  import * as echarts from 'echarts'

  export default {
    name: 'Temperatures',
    data() {
      return {
        intervalId: null,
        temperaturesData: [],
        timeData: [],
        myChart: null,
        option: {
          xAxis: {
            type: 'category',
            data: []
          },
          yAxis: {
            type: 'value'
          },
          series: [
            {
              data: [],
              type: 'line',
              smooth: true,
              label : {normal:{show: true}}
            }
          ]
        }
      }
    },
    methods: {
      initData() {
        // 基于准备好的dom，初始化echarts实例
        this.myChart = echarts.init(document.getElementById('myChart'))
        // 绘制图表
        this.myChart.setOption(this.option);
      },
      getData() {
        this.dataRefresh()
      },
      dataRefresh() {
        if (this.intervalId != null) {
          return;
        }
        //获取数据，1秒调用后端接口一次

        this.intervalId = setInterval(() => {
          this.$http.get("temperature/getPerSecond").then(res => {

            if (this.temperaturesData.length >= 10) {
              this.temperaturesData.shift()
              this.timeData.shift()
              this.temperaturesData.push(res.data)
              this.timeData.push(new Date().toLocaleTimeString())
            }else {
              this.temperaturesData.push(res.data)
              this.timeData.push(new Date().toLocaleTimeString())
            }
            this.myChart.setOption(
              {
                xAxis: {
                  name: '时间: ' + new Date().toLocaleDateString(),
                  type: 'category',
                  data: this.timeData
                },
                yAxis: {
                  name: '温度（°C）',
                  type: 'value'
                },
                series: [
                  {
                    data: this.temperaturesData,
                    type: 'line',
                    smooth: true,
                    label : {normal:{show: true}}
                  }
                ]
              }
            );
            console.log(this.temperaturesData)
          })
        }, 1000);
      },
      clear() {
        clearInterval(this.intervalId);
        this.intervalId = null;
      }
    },
    mounted() {
      console.log(this.option)
      this.getData()
      this.initData()

    }
  }


</script>

<style scoped>

</style>
