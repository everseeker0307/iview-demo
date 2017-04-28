<template>
	<div>
		<Row>
			<Col>
				<template v-if="this.page == '1'">
					<SaledSum></SaledSum>
				</template>
				<template v-if="this.page == '2-1'">
					<ForsaleZZSum></ForsaleZZSum>
				</template>
				<template v-if="this.page == '2-2'">
					<ForsaleBOASum></ForsaleBOASum>
				</template>
				<template v-if="this.page == '3-1'">
					<LimitedZZSum></LimitedZZSum>
				</template>
				<template v-if="this.page == '3-2'">
					<LimitedBOASum></LimitedBOASum>
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