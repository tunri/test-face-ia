<template>
	<div class="py-8">
		<v-container class="mf-container">
			<v-row>
				<v-col cols="12">
					<v-card class="pa-6" color="#eaeaea" elevation="0">
						<v-card-title>
							Validador de Fotos con Inteligencia Artificial
						</v-card-title>
						<v-card-text>
							<p>
								Esta aplicación tiene un algoritmo de Inteligencia Artificial
								desarrollado a tráves de un modelo de Machine Learning
								previamente entrenado. Puede validar las siguientes
								caracteristicas:
							</p>
							<ul>
								<li>Validación de tamaño entre 50 y 600 KB</li>
								<li>Dimensiones máximas de 600x600 pixeles</li>
								<li>Validación de fondo blanco</li>
								<li>Validación de rostro centrado y de frente</li>
								<li>Validación de gestos</li>
								<li>Validación de boca abierta</li>
								<li>Validación de sombrea en los ojos</li>
							</ul>
						</v-card-text>
					</v-card>
				</v-col>
				<v-col cols="6">
					<v-row>
						<v-col cols="12">
							<v-card color="#eee" min-height="200" elevation="0">
								<v-card-text>
									<v-img max-height="600" :src="previewImage"> </v-img>
								</v-card-text>
							</v-card>
						</v-col>
					</v-row>
					<v-row>
						<v-col cols="6">
							<v-btn block color="info" large @click="handleFileImport">
								seleccionar foto
							</v-btn>
							<input
								ref="uploader"
								class="d-none"
								type="file"
								@change="onFileChanged"
							/>
						</v-col>
						<v-col cols="6">
							<v-btn block color="success" large @click="onSubmit">
								procesar foto
							</v-btn>
						</v-col>
					</v-row>
				</v-col>
				<v-col cols="6">
					<div class="pa-8 text-center" v-if="loading">
						<v-progress-circular
							indeterminate
							color="primary"
						></v-progress-circular>
					</div>
					<template v-else>
						<v-alert
							border="left"
							colored-border
							:color="result.color"
							elevation="2"
							v-if="result"
						>
							<v-card elevation="0">
								<v-card-title :class="`pt-0 ${result.color}--text`">
									{{ result.title }}
								</v-card-title>
								<v-card-text>
									<ul>
										<li
											class="body-1 black--text"
											v-for="item in result.specs"
											:key="item.id"
										>
											{{ item.text }}
										</li>
									</ul>
								</v-card-text>
							</v-card>
						</v-alert>
					</template>
				</v-col>
			</v-row>
		</v-container>
	</div>
</template>

<script>
// import axios from "axios";

const DATA = {
	validation: false,
	specs: [
		{
			id: 1,
			text: "Fondo no es blanco",
		},
		{
			id: 2,
			text: "Rostro no esta centrado",
		},
		{
			id: 3,
			text: "Rostro tiene lentes",
		},
	],
};

export default {
	name: "Home",

	components: {},

	data: () => ({
		previewImage: null,
		color: null,
		loading: false,
		result: null,
	}),

	methods: {
		async onSubmit() {
			if (this.previewImage) {
				this.loading = true;
				this.result = null;
				try {
					const Response = await this.request();
					const data = {};

					data.title = Response.validation
						? "La foto cumple las validaciones"
						: "No cumple validación";
					data.specs = Response.specs;
					data.color = Response.validation ? "success" : "error";

					this.result = { ...data };
				} catch (error) {
					console.log(error);
				} finally {
					this.loading = false;
				}
			}
		},
		handleFileImport() {
			this.$refs.uploader.click();
		},
		onFileChanged(e) {
			this.createImage(e.target.files[0]);
		},
		createImage(file) {
			const reader = new FileReader();

			reader.onload = (e) => {
				this.previewImage = e.target.result;
			};

			reader.readAsDataURL(file);
		},
		request() {
			return new Promise((resolve) => setTimeout(resolve, 2000, DATA));
		},
	},
};
</script>
<style lang="scss">
.mf-container {
	max-width: 1064px;
}
</style>
