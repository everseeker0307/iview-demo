<template>
	<Table height="500" :columns="column_deal" :data="data_deal"></Table>
</template>

<script>
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
				data_deal: [
					{
						houseName: '四季景园',
						dealNum: 20
					},
					{
						houseName: '橡树湾地',
						dealNum: 12
					},
					{
						houseName: '洋溪雅苑',
						dealNum: 118
					}
				]
			}
		}
	}
</script>