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
				成交量
			</Menu-item>
            <Submenu name="2">
                <template slot="title">
                    <Icon type="ios-paper"></Icon>
                        在售库存
                </template>
                <Menu-item name="2-1">
                    <Icon type="ios-paper"></Icon>
                    在售住宅库存
                </Menu-item>
                <Menu-item name="2-2">
                    <Icon type="ios-paper"></Icon>
                    在售商业办公库存
                </Menu-item>
            </Submenu>
            <Submenu name="3">
                <template slot="title">
                    <Icon type="ios-paper"></Icon>
                        限售库存
                </template>
                <Menu-item name="3-1">
                    <Icon type="ios-paper"></Icon>
                    限售住宅库存
                </Menu-item>
                <Menu-item name="3-2">
                    <Icon type="ios-paper"></Icon>
                    限售商业办公库存
                </Menu-item>
            </Submenu>
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