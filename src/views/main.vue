<template>
	<div>
		<Row>
			<Col>
				<template v-if="this.page == '1'">
					<SaledSum></SaledSum>
				</template>
			</Col>
			<Col style="width: auto; height: auto">
				<template v-if="this.page == '2'">
					<TodayDealDetails></TodayDealDetails>
				</template>
			</Col>
		</Row>
	</div>
</template>
<script>
	export default {
		props: ['page'],
		mounted() {
            console.log("page=" + this.page);
        },
	}
</script>