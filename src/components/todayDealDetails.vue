<template>
    <div>
        <Table v-if="!loading" :columns="column_deal" :data="data_deal"></Table>
        <Spin v-if="loading" style="margin: 80px">
            <Icon type="load-c" size=24 class="spin-icon-load"></Icon>
            <div>Loading...</div>
        </Spin>
    </div>
</template>
<script>
    import axios from 'axios';
    import Qs from 'qs';
    export default {
        props: ['selDate', 'selInterval'],
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
                loading: true
            }
        },
        created() {
            var that = this;
            this.$on('getTodayDealDetails', function(param1) {
                console.log('param=' + param1);
                //post默认发送的参数为json格式，通过Qs.stringify()转换为form-data格式
                axios.post('http://localhost:8080/getTodayDealDetails', Qs.stringify({
                        today: param1
                    })
                )
                .then(function(resp) {
                    console.log(resp.data);
                    that.loading = false;
                    that.data_deal = resp.data;
                })
                .catch(function(resp) {
                    console.log(resp);
                })
            });
            // this.getForsaleHouseNumSum();
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
            // getTodayDealDetails(param1) {
            //     //post默认发送的参数为json格式，通过Qs.stringify()转换为form-data格式
            //     axios.post('http://localhost:8080/getTodayDealDetails', Qs.stringify({
            //             today: param1
            //         })
            //     )
            //     .then(function(resp) {
            //         console.log(resp.data);
            //         that.loading = false;
            //         that.data_deal = resp.data;
            //     })
            //     .catch(function(resp) {
            //         console.log(resp);
            //     })
            // }
        }
    }
</script>