<template>
	<form @submit.prevent="addMatch" class="card-panel col s12">
		<div class="row">
			<h2 class="center deep-purple-text">Добавить матч</h2></div>
		<div class="row">
			<div class="field col s6">
				<label for="first_team">Название команды 1*</label>
				<input id="first_team" type="text" v-model="first_team">
			</div>
			<div class="field col s6">
				<label for="second_team">Название команды 2*</label>
				<input id="second_team" type="text" v-model="second_team">
			</div>
		</div>
		<div class="row">
			<div class="field col s12">
				<label for="date">Дата матча*</label>
				<input id="date" type="text" v-mask="{mask: '99-99-9999', greedy: true}" v-model="date">
			</div>
		</div>
		<div class="row">
			<div class="field col s12">
				<label for="info">Дополнительная информация</label>
				<textarea id="info" class="materialize-textarea" v-model="info"></textarea>
			</div>
		</div>

		<div class="row">
			<div class="field col s12">
				<label>
					<input type="checkbox" class="filled-in" v-model="previous"/>
					<span>Предыдущий результат</span>
				</label>
			</div>
		</div>

		<div class="row" v-if="previous">
			<div class="field col s6">
				<label for="previous_result_first_team">Голов в первом матче команда 1</label>
				<input id="previous_result_first_team" type="text" v-model="previous_result.first_team">
			</div>
			<div class="field col s6">
				<label for="previous_result_second_team">Голов в первом матче команда 2</label>
				<input id="previous_result_second_team" type="text" v-model="previous_result.second_team">
			</div>
		</div>

		<div class="row">
			<div class="field col s12">
				<p v-if="feedback" class="red-text center">{{ feedback }}</p>
			</div>
		</div>

		<div class="row">
			<div class="field col s12 center">
				<button class="btn deep-purple"> Добавить {{ previous }}</button>
			</div>
		</div>

	</form>
</template>

<script>
	import Vue from 'vue'
	import db from '@/firebase/init'

	const VueInputMask = require('vue-inputmask').default
	Vue.use(VueInputMask)


	export default {
		name: "AppAddMatch",
		data: () => ({
			first_team: null,
			second_team: null,
			previous_result: {
				first_team: null,
				second_team: null,
			},
			feedback: null,
			date: null,
			info: null,
			previous: false
		}),
		created() {

		},
		methods: {

			addMatch() {
				let isValid = false;
				if(this.previous) {
					isValid = this.second_team && this.first_team && this.date && this.previous_result.first_team && this.previous_result.first_team
				} else {
					isValid = this.second_team && this.first_team && this.date
				}

				if(isValid) {
					let ref = db.collection('games').doc();

					db.collection('games').add({
						first_team: this.first_team,
						second_team: this.second_team,
						previous_result: this.previous_result,
						date: this.date,
						info: this.info,
						id: ref.id
					}).then(() => {
						this.feedback = 'Матч создан'
						this.$store.dispatch('games/SETGAMES');
					})
					.catch((err) => {
						this.feedback = err.message
					});
				} else {
					this.feedback = 'Одно из обязательных полей не заполнено'
				}
			}
		}
	}
</script>

<style scoped>

</style>