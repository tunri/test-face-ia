<template>
	<v-app>
		<v-app-bar app color="#272a38" dark elevation="0">
			<div class="d-flex align-center">
				<v-img
					alt="Antamina"
					contain
					:src="require('@/assets/logo-antamina.png')"
					transition="scale-transition"
					width="56"
				/>
			</div>
			<v-spacer></v-spacer>
			<h3>Validador de Fotos con Inteligencia Artificial</h3>
            <v-spacer></v-spacer>
		</v-app-bar>

		<v-main>
			<router-view />
		</v-main>

		<v-footer padless>
			<v-col class="text-center" cols="12">
				{{ new Date().getFullYear() }} <strong></strong>
			</v-col>
		</v-footer>
	</v-app>
</template>

<script>
export default {
	name: "App",

	data: () => ({
		//
	}),
};
</script>
