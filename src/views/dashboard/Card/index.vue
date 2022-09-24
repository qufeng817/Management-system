<template lang="">
<!-- elementUI 24格网格布局 -->
    <div>
        <el-row :gutter ="10">
          <el-col :span="6">
            <el-card>
                <Detail title="总销售额" count="¥ 126560">
                    <template slot="charts">
                        <span>周同比&nbsp;&nbsp;56.67% <svg t="1662518703365" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="12314" width="16" height="16"><path d="M960 704L512 256l-448 448z" fill="#1afa29" p-id="12315"></path></svg></span>
                        <span>日同比&nbsp;&nbsp;19.96% <svg t="1662518642977" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="11256" width="16" height="16"><path d="M840.4 300H183.6c-19.7 0-30.7 20.8-18.5 35l328.4 380.8c9.4 10.9 27.5 10.9 37 0L858.9 335c12.2-14.2 1.2-35-18.5-35z" p-id="11257" fill="#d81e06"></path></svg></span>
                    </template>
                    <template slot="footer">
                        <span>日销售额¥ 12423</span>
                    </template>
                </Detail>
            </el-card>
          </el-col>
          <el-col :span="6">
            <el-card>
                <Detail title="访问量" count="88460">
                    <template slot="charts">
                        <lineChart></lineChart>
                    </template>    
                    <template slot="footer">
                        <span>日访问量 12423</span>
                    </template>
                </Detail>
            </el-card>
          </el-col>
          <el-col :span="6">
            <el-card>
                <Detail title="支付笔数" count="88460">
                    <template slot="charts">
                        <barCharts></barCharts>
                    </template>  
                    <template slot="footer">
                        <span>转换率 65%</span>
                    </template>                    
                </Detail>
            </el-card>
          </el-col>
          <el-col :span="6">
            <el-card>
                <Detail title="运营活动效果" count="78%">
                    <template slot="charts">
                        <progressCharts></progressCharts>
                    </template>
                    <template slot="footer">
                        <span>周同比&nbsp;&nbsp;56.67% <svg t="1662518703365" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="12314" width="16" height="16"><path d="M960 704L512 256l-448 448z" fill="#1afa29" p-id="12315"></path></svg></span>
                        <span>日同比&nbsp;&nbsp;19.96% <svg t="1662518642977" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="11256" width="16" height="16"><path d="M840.4 300H183.6c-19.7 0-30.7 20.8-18.5 35l328.4 380.8c9.4 10.9 27.5 10.9 37 0L858.9 335c12.2-14.2 1.2-35-18.5-35z" p-id="11257" fill="#d81e06"></path></svg></span>
                    </template>
                </Detail>
            </el-card>
          </el-col>
        </el-row>
       
    </div>
</template>
<script>
import Detail from './Detail/index.vue'
import lineChart from './lineChart/index.vue'
import barCharts from './barCharts/index.vue'
import progressCharts from './progressCharts/index.vue'

export default {
  name: '',
  components:{
    Detail,
    lineChart,
    barCharts,
    progressCharts
  }
  
}
</script>
<style lang="">
    
</style>