<template>
	<div class="py-8">
		<v-container class="mf-container">
			<v-row>
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
						<v-alert border="left" colored-border elevation="2" v-if="response">
							<v-card elevation="0">
								<v-card-title :class="`pt-0`"> Resultado </v-card-title>
								<v-card-text>
									<ul class="list-style-none pa-0">
										<li v-if="isSubmitted">
											<ItemValidationVue :isCorrect="response.bg_white">
												Fondo Blanco
											</ItemValidationVue>
										</li>
										<li v-if="isSubmitted">
											<ItemValidationVue :isCorrect="response.face_centered">
												Rostro centrado y de frente
											</ItemValidationVue>
										</li>
										<li v-if="isSubmitted">
											<ItemValidationVue :isCorrect="response.face_found">
												Rostro encontrado
											</ItemValidationVue>
										</li>
										<li v-if="isSubmitted">
											<ItemValidationVue :isCorrect="response.mouth_open">
												Boca abierta
											</ItemValidationVue>
										</li>
										<li v-if="isSubmitted">
											<div class="body-1 black--text">
												<v-icon color="success">mdi-check-bold</v-icon>
												Rostro <span v-if="!response.glasses_on">no </span>tiene
												lentes
											</div>
											<!-- <ItemValidationVue :isCorrect="response.glasses_on">
												Rostro tiene lentes
											</ItemValidationVue> -->
										</li>
										<li v-if="isSubmitted">
											<ItemValidationVue :isCorrect="validDimensions">
												tiene dimensiones correctas
											</ItemValidationVue>
										</li>
									</ul>
								</v-card-text>
							</v-card>
						</v-alert>
					</template>
				</v-col>
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
			</v-row>
		</v-container>
	</div>
</template>

<script>
import ItemValidationVue from "./ItemValidation.vue";

import axios from "axios";

export default {
	name: "Home",

	components: { ItemValidationVue },

	data: () => ({
		previewImage: null,
		color: null,
		loading: false,
		result: null,
		file: null,
		response: {},
		maxWidth: 600,
		maxHeight: 600,
		maxSize: 600 * 1024,
		isSubmitted: false,
	}),

	computed: {
		validDimensions() {
			const { width, height } = this.response;
			return width <= this.maxWidth && height <= this.maxHeight;
		},
	},

	methods: {
		async onSubmit() {
			this.isSubmitted = true;
			if (this.previewImage) {
				this.loading = true;
				this.result = null;
				try {
					const Response = await this.request();
					this.response = Response.data;
					console.log(this.response);
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
			this.isSubmitted = false;
			this.file = e.target.files[0];
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
			const bodyFormData = new FormData();
			bodyFormData.append("file", this.file);
			return axios.post("http://35.208.36.72:5000/", bodyFormData, {
				headers: {
					Accept: "text/html",
					"Content-Type": "application/x-www-form-urlencoded",
				},
			});
		},
	},
};
</script>
<style lang="scss">
.mf-container {
	max-width: 1064px;
}
.list-style-none {
	list-style: none;
}
</style>
