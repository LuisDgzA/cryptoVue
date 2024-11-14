<script setup>
	import { ref, onMounted, reactive } from 'vue';

	import Alerta from './components/Alerta.vue';

	 const monedas = ref([
		{ codigo: 'USD', texto: 'Dolar de Estados Unidos'},
		{ codigo: 'MXN', texto: 'Peso Mexicano'},
		{ codigo: 'EUR', texto: 'Euro'},
		{ codigo: 'GBP', texto: 'Libra Esterlina'},
	])

	const criptomonedas = ref([])

	const error = ref('');

	onMounted(() => {
		fetch('https://min-api.cryptocompare.com/data/top/mktcapfull?limit=10&tsym=USD')
			.then(response => response.json())
			.then(({Data}) => {
				// console.log(Data)
				criptomonedas.value = Data
			})
	})

	const valoresCotizar = reactive({
		moneda: '',
		cripto: ''
	})

	const cotizarCrypto = () => {
		if(Object.values(valoresCotizar).includes('')){
			error.value = 'todos los campos son obligatorios'
			console.log('todos los campos son obligatorios')
			return;
		}
		error.value = ''
	}
</script>

<template>
	<div class="contenedor">
		<h1 class="titulo">Cotizador de <span>Criptomonedas</span></h1>
		<div class="contenido">
			<Alerta
				v-if="error">
				{{ error }}
			</Alerta>
			<form
				class="formulario"
				@submit.prevent="cotizarCrypto">
				<div class="campo">
					<label for="moneda">Moneda:</label>
					<select name="" id="moneda" v-model="valoresCotizar.moneda">
						<option value="">Selecciona</option>
						<option v-for="moneda in monedas" :value="moneda.codigo">{{moneda.texto}}</option>
					</select>
				</div>
				<div class="campo">
					<label for="cripto">Criptomoneda:</label>
					<select name="" id="cripto" v-model="valoresCotizar.cripto">
						<option value="">Selecciona</option>
						<option v-for="cripto in criptomonedas" :value="cripto.CoinInfo.Name">{{cripto.CoinInfo.FullName}}</option>
					</select>
				</div>
				<input type="submit" value="Cotizar" />
			</form>
		</div>
	</div>
</template>

<style scoped>
</style>
