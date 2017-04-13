<template>
    <div>
        <Row :gutter="16">
            <Col span="24">
                <div>
                    <MyMenu v-on:selectMenu="changeMenu" ref="ca"></MyMenu>
                </div>
            </Col>
        </Row>
        <Row>
            <Col span="24">
                <Main :page="item"></Main>
            </Col>
        </Row>
    </div>
</template>
<script>
    
    export default {
        data() {
            return {
                item: '1'
            }
        },
        methods: {
            changeMenu: function() {
                this.item = this.$refs.ca.itemname;
            }
        }
    }
</script>
