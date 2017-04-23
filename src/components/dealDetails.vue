<template>
    <div>
        <br>
        <p v-if="!loading" style="font-size: 20px; font-weight: bold;">
            <span>{{showDate}}成交详情:</span>
        </p>
        <br>
        <Table v-if="!loading" :columns="column_deal" :data="data_deal_show"></Table>
        <Spin v-if="loading" style="margin: 80px">
            <Icon type="load-c" size=24 class="spin-icon-load"></Icon>
            <div>Loading...</div>
        </Spin>
        <Page v-if="!loading" :total="total" :current="currentPage" show-total show-elevator @on-change="changePageNum"></Page>
    </div>
</template>
<script>
    import axios from 'axios';
    import Qs from 'qs';
    export default {
        data() {
            return {
                column_deal: [
                    {
                        title: '楼盘名',
                        key: 'houseName'
                    },
                    {
                        title: '成交量',
                        key: 'dealNum'
                    }
                ],
                data_deal: [],
                data_deal_show: [],
                loading: true,
                total: 0,
                currentPage: 1,
                currentDate: new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString(),
                daysInterval: 1,
                showDate: new Date(new Date().getTime() - 24*60*60*1000).toLocaleDateString()
            }
        },
        created() {
            var that = this;
            this.$on('getPeriodDetails', function(param1, param2) {
                //post默认发送的参数为json格式，通过Qs.stringify()转换为form-data格式
                axios.post('http://localhost:8080/getPeriodDetails', Qs.stringify({
                        givenday: param1,
                        daysInterval: param2
                    })
                )
                .then(function(resp) {
                    console.log(resp.data);
                    that.currentDate = param1;
                    that.daysInterval = param2;
                    that.total = resp.data.length;
                    that.loading = false;
                    that.data_deal = resp.data;
                    that.currentPage = 1;
                    if (that.daysInterval == 1)
                        that.showDate = new Date(Date.parse(that.currentDate)).toLocaleDateString();
                    else if (that.daysInterval == 7) {
                        let startDate = new Date(Date.parse(that.currentDate));
                        startDate.setDate(startDate.getDate() - 6);
                        that.showDate = startDate.toLocaleDateString() + " ~ " + new Date(Date.parse(that.currentDate)).toLocaleDateString();
                    } else if (that.daysInterval == 30) {
                        let startDate = new Date(Date.parse(that.currentDate));
                        startDate.setDate(1);
                        that.showDate = startDate.toLocaleDateString() + " ~ " + new Date(Date.parse(that.currentDate)).toLocaleDateString();
                    }
                    that.changePageNum(that.currentPage);
                })
                .catch(function(resp) {
                    console.log(resp);
                })
            });
        },
        methods: {
            getForsaleHouseNumSum() {
                axios.get('http://localhost:8080/getForsaleHouseNumSum', {
                    params: {
                        givenday: new Date().toLocaleDateString()
                    }
                })
                .then(function(resp) {
                    console.log(resp);
                })
                .catch(function(resp) {
                    console.log(resp);
                });
            },
            changePageNum(param) {
                this.currentPage = param;
                this.data_deal_show = [];
                for (let i = 0; i < 10 && i < (this.total+10-param*10); i++) {
                    this.data_deal_show.push(this.data_deal[(param-1)*10+i]);
                }
            }
        }
    }
</script>