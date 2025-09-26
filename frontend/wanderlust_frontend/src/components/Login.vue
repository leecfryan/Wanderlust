<script setup>
import { ref, computed, onMounted, nextTick, watch } from 'vue'

const activeTab = ref('login')
const username = ref('')
const password = ref('')
const email = ref('')
const confirmPassword = ref('')
const errors = ref({})

const validateLogin = (e) => {
  e.preventDefault()
  errors.value = {}

  if (!username.value) errors.value.username = 'Username is required'
  else if (username.value.length < 3) errors.value.username = 'Username must be at least 3 characters'

  if (!password.value) errors.value.password = 'Password is required'
  else if (password.value.length < 6) errors.value.password = 'Password must be at least 6 characters'

  if (Object.keys(errors.value).length === 0) {
    console.log('Login form is valid, proceeding...')
  }
}

const validateRegister = (e) => {
  e.preventDefault()
  errors.value = {}

  if (!username.value) errors.value.username = 'Username is required'
  else if (username.value.length < 3) errors.value.username = 'Username must be at least 3 characters'

  if (!email.value) errors.value.email = 'Email is required'
  else if (!/\S+@\S+\.\S+/.test(email.value)) errors.value.email = 'Please enter a valid email'

  if (!password.value) errors.value.password = 'Password is required'
  else if (password.value.length < 6) errors.value.password = 'Password must be at least 6 characters'

  if (!confirmPassword.value) errors.value.confirmPassword = 'Please confirm your password'
  else if (confirmPassword.value !== password.value) errors.value.confirmPassword = 'Passwords do not match'

  if (Object.keys(errors.value).length === 0) {
    alert(`Registration successful!\n\nUsername: ${username.value}\nEmail: ${email.value}`);
    
    // Reset form
    username.value = ''
    password.value = ''
    email.value = ''
    confirmPassword.value = ''
    
    // Switch to login tab
    switchTab('login')
  }
}

// --- tab switch + field reset ---
const switchTab = (tab) => {
  activeTab.value = tab
  errors.value = {}
  username.value = ''
  password.value = ''
  email.value = ''
  confirmPassword.value = ''
}

// --- slide position ---
const trackStyle = computed(() => ({
  transform: `translateX(${activeTab.value === 'login' ? '0%' : '-50%'})`
}))

// --- touch swipe support ---
const startX = ref(0)
const deltaX = ref(0)
const onTouchStart = (e) => { startX.value = e.touches[0].clientX; deltaX.value = 0 }
const onTouchMove = (e) => { deltaX.value = e.touches[0].clientX - startX.value }
const onTouchEnd = () => {
  const t = 60
  if (deltaX.value <= -t && activeTab.value === 'login') switchTab('register')
  else if (deltaX.value >= t && activeTab.value === 'register') switchTab('login')
  deltaX.value = 0
}

// --- auto height animation ---
const viewportEl = ref(null)
const trackEl = ref(null)
let ro

const measureAndSetHeight = () => {
  if (!viewportEl.value || !trackEl.value) return
  const index = activeTab.value === 'login' ? 0 : 1
  const panels = trackEl.value.children
  if (!panels || !panels[index]) return
  const h = panels[index].getBoundingClientRect().height
  viewportEl.value.style.height = `${h}px`
}

onMounted(() => {
  nextTick(measureAndSetHeight)
  ro = new ResizeObserver(() => measureAndSetHeight())
  ro.observe(trackEl.value)
})

watch(activeTab, async () => {
  await nextTick()
  measureAndSetHeight()
})
</script>

<template>
  <div class="auth-wrap">
    <div class="auth-card" @touchstart="onTouchStart" @touchmove="onTouchMove" @touchend="onTouchEnd">
      <!-- Tabs -->
      <div class="tabs" role="tablist" aria-label="Authentication Tabs">
        <button
          class="tab-btn"
          :class="{ active: activeTab === 'login' }"
          role="tab"
          :aria-selected="activeTab === 'login'"
          @click="switchTab('login')"
        >
          Login
        </button>
        <button
          class="tab-btn"
          :class="{ active: activeTab === 'register' }"
          role="tab"
          :aria-selected="activeTab === 'register'"
          @click="switchTab('register')"
        >
          Register
        </button>
        <span class="active-underline" :class="activeTab"></span>
      </div>

      <!-- Sliding viewport -->
      <div class="viewport" ref="viewportEl">
        <div class="track" :style="trackStyle" ref="trackEl">
          <!-- LOGIN PANEL -->
          <div class="panel">
            <form @submit="validateLogin" class="form">
              <h3>Welcome back</h3>

              <div class="form-group">
                <label for="username">Username</label>
                <input
                  id="username"
                  type="text"
                  v-model="username"
                  :class="{ error: errors.username }"
                  autocomplete="username"
                />
                <span class="error-message" v-if="errors.username">{{ errors.username }}</span>
              </div>

              <div class="form-group">
                <label for="password">Password</label>
                <input
                  id="password"
                  type="password"
                  v-model="password"
                  :class="{ error: errors.password }"
                  autocomplete="current-password"
                />
                <span class="error-message" v-if="errors.password">{{ errors.password }}</span>
              </div>

              <button type="submit" class="primary">Login</button>
            </form>
          </div>

          <!-- REGISTER PANEL -->
          <div class="panel">
            <form @submit="validateRegister" class="form">
              <h3>Create account</h3>

              <div class="form-group">
                <label for="reg-username">Username</label>
                <input
                  id="reg-username"
                  type="text"
                  v-model="username"
                  :class="{ error: errors.username }"
                  autocomplete="username"
                />
                <span class="error-message" v-if="errors.username">{{ errors.username }}</span>
              </div>

              <div class="form-group">
                <label for="email">Email</label>
                <input
                  id="email"
                  type="email"
                  v-model="email"
                  :class="{ error: errors.email }"
                  autocomplete="email"
                />
                <span class="error-message" v-if="errors.email">{{ errors.email }}</span>
              </div>

              <div class="form-group">
                <label for="reg-password">Password</label>
                <input
                  id="reg-password"
                  type="password"
                  v-model="password"
                  :class="{ error: errors.password }"
                  autocomplete="new-password"
                />
                <span class="error-message" v-if="errors.password">{{ errors.password }}</span>
              </div>

              <div class="form-group">
                <label for="confirm-password">Confirm Password</label>
                <input
                  id="confirm-password"
                  type="password"
                  v-model="confirmPassword"
                  :class="{ error: errors.confirmPassword }"
                  autocomplete="new-password"
                />
                <span class="error-message" v-if="errors.confirmPassword">{{ errors.confirmPassword }}</span>
              </div>

              <button type="submit" class="primary">Register</button>
            </form>
          </div>
        </div>
      </div>
      <!-- /viewport -->
    </div>
  </div>
</template>

<style scoped>
.auth-wrap {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 24px;
  background:
    radial-gradient(1200px 600px at 20% 10%, rgba(106,141,255,0.07) 0%, transparent 60%),
    radial-gradient(1000px 600px at 80% 90%, rgba(78,110,219,0.07) 0%, transparent 60%),
    var(--background-color);
}

.auth-card {
  width: 100%;
  max-width: 480px;
  background: #fff;
  border-radius: 16px;
  box-shadow: 0 15px 60px rgba(0,0,0,0.08);
  overflow: hidden;
  border: 1px solid rgba(0,0,0,0.06);
  display: flex;
  flex-direction: column;
}

/* Tabs */
.tabs {
  position: relative;
  display: grid;
  grid-template-columns: 1fr 1fr;
  background: linear-gradient(180deg, #ffffff, #f9fafb);
}

.tab-btn {
  padding: 16px 20px;
  background: transparent;
  border: none;
  font-weight: 700;
  color: rgba(0,0,0,0.5);
  cursor: pointer;
  transition: color .25s ease;
}
.tab-btn.active { color: var(--text-color); }

.active-underline {
  position: absolute;
  bottom: 0;
  left: 0;
  height: 3px;
  width: 50%;
  background: var(--primary-color);
  transition: transform .35s cubic-bezier(.22,.61,.36,1);
}
.active-underline.register { transform: translateX(100%); }
.active-underline.login { transform: translateX(0%); }

/* Sliding viewport + track */
.viewport {
  overflow: hidden;
  position: relative;
  transition: height .35s cubic-bezier(.22,.61,.36,1);
  min-height: 400px;
}

.track {
  width: 200%;
  display: flex;
  transition: transform .45s cubic-bezier(.22,.61,.36,1);
  will-change: transform;
}

.panel {
  width: 50%;
  padding: 28px 28px 32px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.form {
  width: 100%;
  max-width: 320px;
  margin: 0 auto; /* Center the form */
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.form h3 {
  text-align: center;
  margin-bottom: 18px;
  font-size: 1.25rem;
  color: var(--text-color);
}

button.primary {
  width: 100%;
  padding: 0.75rem;
  background: var(--primary-color);
  color: #fff;
  border: none;
  border-radius: 10px;
  font-weight: 700;
  cursor: pointer;
  transition: transform .05s ease, background .2s ease;
}
button.primary:hover { background: var(--primary-hover); }
button.primary:active { transform: translateY(1px); }

input:focus {
  box-shadow: 0 0 0 6px rgba(106, 141, 255, 0.15);
  border-color: var(--primary-color);
}

@media (max-width: 420px) {
  .panel { padding: 22px 18px 26px; }
}
</style>
