<template>
	<div>
        <Row>
            <Col span="16">
                <div align="center" style="margin-top: 20px">
                <Radio-group v-model="radioButton" type="button" @on-change="changeItem">
                    <Radio label="按日统计"></Radio>
                    <Radio label="按周统计"></Radio>
                    <Radio label="按月统计"></Radio>
                </Radio-group>
                </div>
                <Spin v-if="loading" style="margin-top: 20px">
                    <Icon type="load-c" size=24 class="spin-icon-load"></Icon>
                    <div>Loading...</div>
                </Spin>
                <Spin v-if="!loading" style="margin-top: 20px; height: 45px"></Spin>
                <div id="showChart" style="width: 900px; height: 500px"></div>
            </Col>
            <Col span="8">
                <TodayDealDetails ref="tdd"></TodayDealDetails>
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
    			radioButton: '按日统计'
    		}
    	},
        mounted() {
            this.selDate = new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString();
            this.getDailySaledSum();
            this.$refs.tdd.$emit('getTodayDealDetails', new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString());
        },
        methods: {
            changeItem(param) {
                this.radioButton = param;
                this.saledData = [];
                this.loading = true;
                this.getDailySaledSum();
            },
            getDailySaledSum() {
                const that = this;
                let dateSpace = 1;
                if (this.radioButton === "按日统计")
                    dateSpace = 1;
                else if (this.radioButton === "按周统计")
                    dateSpace = 7;
                else if (this.radioButton === "按月统计")
                    dateSpace = 30;
                axios.post('http://localhost:8080/getIntervalSaledHouseNumSum', Qs.stringify({
                        startDay: '',
                        endDay: new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString(),
                        interval: dateSpace
                    })
                )
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
            				show: true
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
            			data: this.saledData
            		}]
            	};
            	myChart.setOption(option);
                const that = this;
            	myChart.on('click', function(params) {
			    	console.log(params.name);
                    that.selDate = params.name;
                    that.$refs.tdd.$emit('getTodayDealDetails', params.name);
			    });
	        }
        }
    }
</script>
