<template>
	<div>
        <Row :gutter="16">
            <Col span="24">
                <div align="center" style="margin-top: 20px">
                <Radio-group v-model="radioButton" type="button" @on-change="changeItem">
                    <Radio label="按周统计"></Radio>
                    <Radio label="按月统计"></Radio>
                </Radio-group>
                </div>
                <Spin v-if="loading" style="margin-top: 16px; height: 40px">
                    <Icon type="load-c" size=24 class="spin-icon-load"></Icon>
                    <div>Loading...</div>
                </Spin>
                <div v-if="!loading" style="margin-top: 16px; height: 40px"></div>
                <div id="showChart" style="width: 1200px; height: 500px"></div>
            </Col>
        </Row>
	</div>
</template>
<script>
    import axios from 'axios';
    import Qs from 'qs';
    import echarts from 'echarts';
    export default {
        data() {
            return {
                data: [],   //ajax请求后台返回的数据, 格式为[{"date": '2017/04/12', "zzNumSum": 7833}, {...}]
                forsaleData: [],    //echarts图表需要的数据, 由data转化得到
                loading: true,
                radioButton: '按周统计',
                daysInterval: 7
            }
        },
        mounted() {
            this.getForsaleSum();
        },
        methods: {
            changeItem(param) {
                this.radioButton = param;
                this.forsaleData = [];
                this.loading = true;
                this.getForsaleSum();
            },
            getForsaleSum() {
                const that = this;
                if (this.radioButton === "按周统计")
                    this.daysInterval = 7;
                else if (this.radioButton === "按月统计")
                    this.daysInterval = 30;
                axios.post('http://103.238.227.120:8080/getForsaleZhuzhaiNumSum', Qs.stringify({
                    startDay: '',
                    endDay: new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString(),
                    interval: that.daysInterval  
                }))
                .then(function(resp) {
                    console.log(resp);
                    if (resp.status === 200) {
                        that.loading = false;
                        that.data = resp.data;
                        that.drawChart();
                    }
                })
                .catch(function(resp) {
                    console.log(resp);
                });
            },
            drawChart() {
                const myChart = echarts.init(document.getElementById('showChart'));
                let minNum = this.data[0]["zzNumSum"];
                for (let each in this.data) {
                    let date = this.data[each]["date"];
                    this.forsaleData.push({"name": date, "value": [date, this.data[each]["zzNumSum"]]});
                    if (minNum > this.data[each]["zzNumSum"])
                        minNum = this.data[each]["zzNumSum"];
                }
                const option = {
                    title: {
                        text: '住宅库存统计(估计值，仅供参考)',
                        left: '30%'
                    },
                    tooltip: {
                        trigger: 'axis',
                        triggerOn: 'mousemove',
                        formatter: function(params) {
                            params = params[0];
                            return params.name + ": " + params.value[1];
                        },
                        axisPointer: {
                            animation: false
                        }
                    },
                    xAxis: {
                        type: 'time',
                        name: '日期',
                        splitLine: {
                            show: false
                        }
                    },
                    yAxis: {
                        type: 'value',
                        name: '在售住宅库存',
                        boundaryGap: [0, "20%"],
                        min: minNum - 100,
                        minInterval: 1,
                        splitLine: {
                            show: true
                        }
                    },
                    series: [{
                        name: '服务器数据',
                        type: 'line',
                        symbolSize: 10,
                        showSymbol: true,
                        hoverAnimation: false,
                        smooth: true,
                        data: this.forsaleData
                    }]
                };
                myChart.setOption(option);
            }
        }
    }
</script>