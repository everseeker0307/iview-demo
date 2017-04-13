<style scoped>
    .layout{
        border: 1px solid #d7dde4;
        background: #f5f7f9;
    }
    .layout-logo{
        width: 100px;
        height: 30px;
        background: #5b6270;
        border-radius: 3px;
        float: left;
        position: relative;
        top: 15px;
        left: 20px;
    }
    .layout-nav{
        width: 420px;
        margin: 0 auto;
    }
</style>
<template>
<div class="layout">
	<Menu mode="horizontal" :theme="theme2" @on-select="selectMenu" active-name="1">
		<div class="layout-logo"></div>
		<div class="layout-nav">
			<Menu-item name="1">
				<Icon type="ios-navigate"></Icon>
				成交量趋势
			</Menu-item>
			<Menu-item name="2">
				<Icon type="ios-paper"></Icon>
				成交详情
			</Menu-item>
			<Menu-item name="3">
				<Icon type="ios-keypad"></Icon>
				库存趋势
			</Menu-item>
		</div>
	</Menu>
</div>
</template>

<script>
	export default {
		data () {
            return {
                theme2: 'dark',
                itemname: '1'
            }
        },
        methods: {
        	selectMenu (itemname) {
        		console.log(itemname);
        		this.itemname = itemname;
        		this.$emit('selectMenu');
        	}
        }
	}
</script>