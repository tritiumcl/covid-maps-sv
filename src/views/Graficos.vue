<template>
  <div class="home">
  	<div class="grid-graficos">
			<GraficoCasosDiarios style="grid-area:a" tipoCasos='0'/>
			<GraficoCasosDiarios style="grid-area:b" tipoCasos='2' mostrarPromedioChk="true"/>
			<GraficoCasosDiarios style="grid-area:c" tipoCasos='1' mostrarPromedioVar="true" mostrarPromedioChk="true"/>
			<GraficoMuertes style="grid-area:d" />
			<GraficoPruebas style="grid-area:e" />
			<GraficoResumenDiario style="grid-area:f" />
  	</div>
		<Footer />
  </div>
</template>

<script lang="ts">
// @ is an alias to /src
import Vue from 'vue';
import Component from 'vue-class-component'
import Footer from '@/components/Footer.vue'
import GraficoResumenDiario from '@/components/GraficoResumenDiario.vue'
import GraficoCasosDiarios from '@/components/GraficoCasosDiarios.vue'
import GraficoMuertes from '@/components/GraficoMuertes.vue';
import GraficoPruebas from '@/components/GraficoPruebas.vue';
import { obtenerJson, sleep } from '../tools/tools';
import { CasosDiarios } from '@/model/geo';
import store from '@/tools/store'
import Chart, { ChartDataSets, ChartOptions } from 'charts.vue2';
import { MyDataset } from '@/tools/graphs_tools';

@Component({
	name: 'Gráficos',
	components: {Footer, GraficoResumenDiario, GraficoCasosDiarios, GraficoMuertes, GraficoPruebas, Chart}
})
export default class Graficos extends Vue {
		
	async init(){
		const fechasCasos = await store.getFechasCasos();
		const casosDiarios = await store.obtenerCasosDiarios(fechasCasos[fechasCasos.length - 1],'reload');

		const histCasosMuni =  [];
		const params = {method:'get', mode: 'cors', cache: 'reload'} as RequestInit;
		for(let i = 0; i < 8; i++){
			histCasosMuni.push(store.getHistCasosDiariosMunicipio(casosDiarios[i].id_municipio));
		}

		await Promise.all(histCasosMuni);
	}

	created(){
		this.init().catch(err => console.log(err));
	}
}
</script>

<style scoped>
.copy {
  font-size: x-small;
}
.grid-graficos {
	display: grid;
	grid-template:
		"a a" 550px
		"b c"
		"d e"
		"f g" /
		50% 50%;
	padding-top: 50px;
}

@media (max-width: 600px){
	.grid-graficos {
		grid-template: 
		"a"
		"b"
		"c"
		"d"
		"e"
		"f"/
		auto;
	}
}
</style>