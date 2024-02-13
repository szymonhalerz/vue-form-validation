<template>
	<div class="wrapper">
		<form @submit.prevent="submitForm">
			<h1>Zarejestruj się!</h1>
			<div class="form-box">
				<input v-model="state.username" type="text" id="username" placeholder="Podaj nazwę użytkownika" />
				<p v-if="v$.username.$error" class="error-text">
					{{ v$.username.$errors[0].$message }}
				</p>
			</div>
			<div class="form-box">
				<input v-model="state.password" type="password" id="password" placeholder="Podaj hasło" />
				<p v-if="v$.password.$error" class="error-text">
					{{ v$.password.$errors[0].$message }}
				</p>
			</div>
			<div class="form-box">
				<input v-model="state.confirmPassword" type="password" id="password2" placeholder="Powtórz hasło" />
				<p v-if="v$.confirmPassword.$error" class="error-text">
					{{ v$.confirmPassword.$errors[0].$message }}
				</p>
			</div>
			<div class="form-box">
				<input v-model="state.email" type="email" id="email" placeholder="Podaj adres mailowy" />
				<p v-if="v$.email.$error" class="error-text">
					{{ v$.email.$errors[0].$message }}
				</p>
			</div>
			<div class="control-buttons">
				<button @click="clearForm" class="clear" type="reset">Wyczyść</button>
				<button class="send" type="submit">Wyślij</button>
			</div>
		</form>
		<div class="popup" :class="{ 'show-popup': popupStatus === true }">
			<p>Formularz został poprawnie wysłany!</p>
			<button @click="closePopup" class="close">Zamknij</button>
		</div>
	</div>
</template>

<script setup>
import useValidate from '@vuelidate/core'
import { required, minLength, email, sameAs, helpers } from '@vuelidate/validators'
import { ref, reactive, computed } from 'vue'

const state = reactive({
	username: '',
	email: '',
	password: '',
	confirmPassword: '',
})

const regPass = value => /^(?=.*[A-Z])(?=.*[\d])(?=.*[\W_]).{8,}$/.test(value)

const popupStatus = ref(false)

const rules = computed(() => {
	return {
		username: {
			required: helpers.withMessage('Nazwa użytkownika jest wymagana!', required),
			minLength: helpers.withMessage(
				`Nazwa użytkownika jest za krótka, minimalna długość to ${5} znaków!`,
				minLength(5)
			),
		},
		password: {
			required: helpers.withMessage('Hasło jest wymagane!', required),
			minLength: helpers.withMessage(`Hasło jest za krótkie minimalna to długość ${8} znaków!`, minLength(8)),
			regPass: helpers.withMessage(
				'Hasło jest za słabe, musisz użyć co najmniej jednej dużej litery, cyfry i znaku specjalnego.',
				regPass
			),
		},
		confirmPassword: {
			required: helpers.withMessage('Potwierdzenie hasła jest wymagane!', required),
			sameAs: helpers.withMessage('Hasła nie są takie same!', sameAs(state.password)),
		},
		email: {
			required: helpers.withMessage('Email jest wymagane!', required),
			email: helpers.withMessage('Email jest niepoprawny!', email),
		},
	}
})

const v$ = useValidate(rules, state)

const clearForm = () => {
	state.username = ''
	state.email = ''
	state.password = ''
	state.confirmPassword = ''
	v$.value.$reset()
}

const submitForm = () => {
	v$.value.$validate()
	if (!v$.value.$error) {
		clearForm()
		popupStatus.value = true
	}
}

const closePopup = () => {
	popupStatus.value = false
}
</script>
