<template>
	<div>
        <Row :gutter="16">
            <Col span="16">
                <div align="center" style="margin-top: 20px">
                    <Radio-group v-model="radioButton" type="button" @on-change="changeItem">
                        <Radio label="按日统计"></Radio>
                        <Radio label="按周统计"></Radio>
                        <Radio label="按月统计"></Radio>
                    </Radio-group>
                    <span style="margin-top: 40px; font-size: 12px">&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;行政区:&emsp;</span>
                    <Select v-model="region" style="width: 100px; margin-top: 5px" @on-change="changeRegion">
                        <Option v-for="item in regions" :value="item.value" :key="item">{{item.label}}</Option>
                    </Select>
                </div>
                <Spin v-if="loading" style="margin-top: 16px; height: 40px">
                    <Icon type="load-c" size=24 class="spin-icon-load"></Icon>
                    <div>Loading...</div>
                </Spin>
                <div v-if="!loading" style="margin-top: 16px; height: 40px"></div>
                <div id="showChart" style="width: 900px; height: 500px"></div>
            </Col>
            <Col span="8">
                <DealDetails ref="tdd"></DealDetails>
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
    			data: [],
    			saledData: [],
    			loading: true,
    			radioButton: '按周统计',
                daysInterval: 7,
                region: '不限',
                regions: [
                    {
                        value: '不限',
                        label: '不限'
                    },
                    {
                        value: '惠山区',
                        label: '惠山区'
                    },
                    {
                        value: '锡山区',
                        label: '锡山区'
                    },
                    {
                        value: '滨湖区',
                        label: '滨湖区'
                    },
                    {
                        value: '新区',
                        label: '新区'
                    },
                    {
                        value: '南长区',
                        label: '南长区'
                    },
                    {
                        value: '北塘区',
                        label: '北塘区'
                    },
                    {
                        value: '崇安区',
                        label: '崇安区'
                    }
                ]
    		}
    	},
        mounted() {
            this.getDailySaledSum();
        },
        methods: {
            changeItem(param) {
                this.radioButton = param;
                this.saledData = [];
                this.loading = true;
                this.getDailySaledSum();
            },
            changeRegion(param) {
                this.saledData = [];
                this.loading = true;
                this.getDailySaledSum();
            },
            getDailySaledSum() {
                const that = this;
                if (this.radioButton === "按日统计")
                    this.daysInterval = 1;
                else if (this.radioButton === "按周统计")
                    this.daysInterval = 7;
                else if (this.radioButton === "按月统计")
                    this.daysInterval = 30;
                axios({
                    method: 'post',
                    url: 'http://103.238.227.120:8080/getIntervalSaledHouseNumSum',
                    headers: {
                        'Content-Type': 'application/x-www-form-urlencoded'
                    },
                    data: Qs.stringify({
                        startDay: '',
                        endDay: new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString(),
                        interval: that.daysInterval,
                        region: that.region
                    })
                })
                .then(function(resp) {
                    console.log(resp);
                    if (resp.status === 200) {
                    	that.loading = false;
                    	that.data = resp.data;
                    	that.drawChart();
                        that.$refs.tdd.$emit('getPeriodDetails', that.data[that.data.length-1].date, that.daysInterval, that.region);
                    }
                })
                .catch(function(resp) {
                    console.log(resp);
                });
            },
            drawChart() {
                const myChart = echarts.init(document.getElementById('showChart'));
            	for (let each in this.data) {
            		let date = this.data[each]["date"];
            		this.saledData.push({"name": date, "value": [date, this.data[each]["dealNumSum"]]});
            	}
            	const option = {
            		title: {
            			text: '成交量统计',
            			subtext: 'tips: 单击圆点可查看具体成交详情',
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
            			name: '成交套数',
            			boundaryGap: [0, "20%"],
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
            			data: this.saledData
            		}]
            	};
            	myChart.setOption(option);
                const that = this;
            	myChart.on('click', function(params) {
			    	console.log(params.name);
                    that.selDate = params.name;
                    that.$refs.tdd.$emit('getPeriodDetails', params.name, that.daysInterval, that.region);
			    });
	        }
        }
    }
</script>
