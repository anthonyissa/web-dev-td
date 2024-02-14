<script setup lang="ts">
import { ref, reactive, toRefs } from 'vue'

const username = ref('')
const email = ref('')
const password = ref('')
const confirmPassword = ref('')
const errors = reactive({
  username: false,
  email: false,
  password: false,
  confirmPassword: false
})

const validateForm = () => {
  let isValid = true
  errors.username = errors.email = errors.password = errors.confirmPassword = false

  if (!username.value || username.value.length < 6 || username.value.includes(' ')) {
    errors.username = true;
    isValid = false
  }
  if (!email.value || !/\S+@\S+\.\S+/.test(email.value)) {
    errors.email = true;
    isValid = false
  }
  if (!password.value || password.value.length < 6) {
    errors.password = true
    isValid = false
  }
  if (password.value !== confirmPassword.value) {
    errors.confirmPassword = true
    isValid = false
  }

  return isValid
}

const signup = () => {
  if (validateForm()) {
    sendRegistrationRequest()
    username.value = ''
    email.value = ''
    password.value = ''
  }
}

const sendRegistrationRequest = async () => {
  try {
    const response = await fetch('https://api.example.com/register', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        username: username.value,
        email: email.value,
        password: password.value
      })
    })
    const data = await response.json()
    console.log(data)
  } catch (error) {
    console.error('Error:', error)
  }
}

const handleBlur = (field:string) => {
  if (field === 'username') {
    errors.username = username.value.length < 6 || username.value.includes(' ')
  }
  if (field === 'email') {
    errors.email = !email.value || !/\S+@\S+\.\S+/.test(email.value)
  }
  if (field === 'password') {
    errors.password = !password.value || password.value.length < 6
  }
  if (field === 'confirmPassword') {
    errors.confirmPassword = password.value !== confirmPassword.value
  }
}
</script>



<template>
  <div>
      <h1>REGISTER</h1>
      <form @submit.prevent="signup">
          <div>
              <label for="username">Username</label>
              <input type="text" id="username" v-model="username" @blur="handleBlur('username')" />
              <p class="error-msg" v-if="errors.username">Invalid Username</p>
          </div>
          <div>
              <label for="email">Email</label>
              <input type="email" id="email" v-model="email" @blur="handleBlur('email')" />
              <p class="error-msg" v-if="errors.email">Invalid Email</p>
          </div>
          <div>
              <label for="password">Password</label>
              <input type="password" id="password" v-model="password" @blur="handleBlur('password')" />
              <p class="error-msg" v-if="errors.password">Invalid Password</p>
          </div>
          <div>
              <label for="confirmPassword">Confirm Password</label>
              <input type="password" id="confirmPassword" v-model="confirmPassword" @blur="handleBlur('confirmPassword')" />
              <p class="error-msg" v-if="errors.confirmPassword">Passwords do not match</p>
          </div>
          <button type="submit">Signup</button>
      </form>
  </div>
</template>

<style scoped>
header {
  line-height: 1.5;
}

h1 {
  text-align: center;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

form div {
  display: flex;
  flex-direction: column;
  margin-bottom: 1rem;
}
.error-msg {
  color: red;
  margin-top: 0.5rem;
}

button {
  width: 100%;
  font-size: 1rem;
  padding: 10px;
  margin-top: 1rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
