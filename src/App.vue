<script setup>
	import { ref, onMounted, reactive, computed } from 'vue';

	import Alerta from './components/Alerta.vue';

	 const monedas = ref([
		{ codigo: 'USD', texto: 'Dolar de Estados Unidos'},
		{ codigo: 'MXN', texto: 'Peso Mexicano'},
		{ codigo: 'EUR', texto: 'Euro'},
		{ codigo: 'GBP', texto: 'Libra Esterlina'},
	])

	const criptomonedas = ref([])

	const error = ref('');

	const cotizacion = ref({})

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
		obtenerCotizacion()
	}

	const obtenerCotizacion = async () => {
		const { moneda, cripto } = valoresCotizar;
		const url = `https://min-api.cryptocompare.com/data/pricemultifull?fsyms=${cripto}&tsyms=${moneda}`
		console.log(url)
		const respuesta = await fetch(url)
		const data = await respuesta.json()
		console.log(data.DISPLAY[cripto][moneda])		
		cotizacion.value = data.DISPLAY[cripto][moneda]
	}

	const isCotizacionEmpty = computed(()=> {
		return Object.entries(cotizacion.value).length > 0
	})
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
			<div class="contedor-resultado" v-if="isCotizacionEmpty">
				<h2>cotización</h2>
				<div class="resultado">
					<img :src="'https://cryptocompare.com'+cotizacion.IMAGEURL" alt="imagen_crypto">
					<div>
						<p>El precio es de: <span>{{ cotizacion.PRICE }}</span></p>
						<p>El precio más alto del día: <span>{{ cotizacion.HIGHDAY }}</span></p>
						<p>El precio más bajo del día: <span>{{ cotizacion.LOWDAY }}</span></p>
						<p>Variación 24 hrs: <span>{{ cotizacion.CHANGEPCT24HOUR }}%</span></p>
						<p>Última actualización: <span>{{ cotizacion.LASTUPDATE }}</span></p>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<style scoped>
</style>
