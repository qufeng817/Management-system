<template lang="">
    <el-card class="box-card" style="margin: 10px 0">
        <!-- 头部 -->
        <div slot="header" class="clearfix">
            <!--  v-model="activeName" @tab-click="handleClick" -->
            <el-tabs class="tab" v-model="activeName" >
              <el-tab-pane label="销售额" name="sale"></el-tab-pane>
              <el-tab-pane label="访问量" name="visite"></el-tab-pane>
            </el-tabs>
            <!-- 头部右侧 -->
            <div class="right">
                <span @click="setDay">今日</span>
                <span @click="setWeek">本周</span>
                <span @click="setMonth">本月</span>
                <span @click="setYear">本年</span>
                <el-date-picker
                  v-model="date"
                  class="date"
                  size="mini"
                  type="daterange"
                  range-separator="-"
                  start-placeholder="开始日期"
                  end-placeholder="结束日期"
                  value-format="yyyy-MM-dd"
                  >
                </el-date-picker>
            </div>
        </div> 
        <!-- 身体 -->
        <el-row :gutter="10">
          <el-col :span="18">
            <div class="charts" ref="charts"></div>
          </el-col>
          <el-col :span="6">
            <div>
                <h3>门店{{title}}排名</h3>
                <ul>
                    <li>
                        <span class="rindex">1</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                    <li>
                        <span class="rindex">2</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                    <li>
                        <span class="rindex">3</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                    <li>
                        <span class="rindex">4</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                    <li>
                        <span >5</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                    <li>
                        <span >6</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                    <li>
                        <span >7</span>
                        <span class="goods">肯德基</span>
                        <span class="rvalue">123456</span>
                    </li>
                </ul>
            </div>
          </el-col>
        </el-row>
    </el-card>
</template>

<script>
import * as echarts from 'echarts'
import dayjs from 'dayjs'
export default {
    name:"",
    data() {
        return {
            activeName:"sale",
            myCharts:null,
            //手机日历的数据
            date:[]
        }
    },

    mounted() {
        this.myCharts = echarts.init(this.$refs.charts)
        this.myCharts.setOption ({
            title:{
                text:"销售额趋势"
            },
         tooltip: {
           trigger: 'axis',
           axisPointer: {
             type: 'shadow'
           }
         },
         grid: {
           left: '3%',
           right: '4%',
           bottom: '3%',
           containLabel: true
         },
         xAxis: [
           {
             type: 'category',
             data: ['1月', '2月', '3月', '4月', '5月', '6月', '7月','8月','9月','10月','11月','12月'],
             axisTick: {
               alignWithLabel: true
             }
           }
         ],
         yAxis: [
           {
             type: 'value'
           }
         ],
         series: [
           {
             name: 'Direct',
             type: 'bar',
             barWidth: '60%',
             data: [10, 52, 200, 334, 390, 330, 220,20,50,100,150,400]
           }
         ]
    })
    },
    computed: {
      title () {
        return this.activeName == 'sale'?'销售额':'访问量'
      }
    },
    //监听属性
    watch:{
      title() {
        this.myCharts.setOption( {
          title: {
            text:this.title
          }
        })
      }
    },
    methods:{
      setDay() {
        //用dayjs去处理日期格式
        const day = dayjs().format('YYYY-MM-DD');
        this.date = [day,day];
      },
      setWeek() {
        const start = dayjs().day(1).format('YYYY-MM-DD');
        const end = dayjs().day(7).format('YYYY-MM-DD');
        this.date = [start,end];
      },
        setMonth() {
        const start = dayjs().startOf('month').format('YYYY-MM-DD');
        const end = dayjs().endOf('month').format('YYYY-MM-DD');
        this.date = [start,end];
      },
      setYear() {
        const start = dayjs().startOf('year').format('YYYY-MM-DD');
        const end = dayjs().endOf('year').format('YYYY-MM-DD');
        this.date = [start,end];        
      }
    }
} 
</script>

<style scoped>
    .clearfix {
        position: relative;
        display: flex;
        justify-content: space-between;
    }
    .tab {
        width: 100%;
    }
    .right {
        position: absolute;
        right: 0;
    }
    .date {
        width: 220px;
        margin: 0px 20px;
    }
    .right span {
        margin: 0px 10px;
    }
    .charts {
        width: 100%;
        height: 300px;
    }
    ul {
        list-style: none;
        width: 100%;
        height: 300px;
        padding: 0;
    }
    ul li {
        height: 8%;
        margin: 10px 0px;
    }
    .goods {
        margin-left: 10px;
    }
    .rindex {
        float: left;
        width: 20px;
        height: 20px;
        padding-left: 5px;
        border-radius: 50%;
        color: #fff;
        background-color: black;
        text-align: center;
        line-height: 20px;
    }
    .rvalue {
        float: right;
    }
</style>