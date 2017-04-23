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