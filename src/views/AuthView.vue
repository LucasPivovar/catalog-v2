<template>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;700&family=Libre+Baskerville:wght@400;700&display=swap" rel="stylesheet">
  <main>
    <div class="auth-container">
      <h1>Crimson Auth</h1>
      <p>{{ activeTab === 'login' ? 'Entre com sua conta para continuar' : 'Crie sua conta para começar' }}</p>
      
      <div class="buttons">
        <a href="#" 
          @click.prevent="activeTab = 'login'" 
          :class="['button', activeTab === 'login' ? 'button-active' : '']">
          Login
        </a>
        <a href="#" 
          @click.prevent="activeTab = 'register'" 
          :class="['button', activeTab === 'register' ? 'button-active' : '']">
          Cadastro
        </a>
      </div>
      
      <component 
        :is="currentComponent" 
        @switch-tab="switchTab"
        @login-success="handleLoginSuccess"
        @register-success="handleRegisterSuccess">
      </component>
      
    </div>
  </main>
</template>

<script>
import '@/assets/css/Auth_content.css';
import '@/assets/css/global.css';
import LoginComponent from '@/components/auth/LoginComponent.vue';
import RegisterComponent from '@/components/auth/RegisterComponent.vue';

export default {
  name: 'AuthView',
  components: {
    LoginComponent,
    RegisterComponent
  },
  data() {
    return {
      activeTab: 'login'
    }
  },
  computed: {
    currentComponent() {
      return this.activeTab === 'login' ? 'LoginComponent' : 'RegisterComponent';
    }
  },
  methods: {
    switchTab(tab) {
      this.activeTab = tab;
    },
    handleLoginSuccess(userData) {
      console.log('Login successful:', userData);
    },
    handleRegisterSuccess(userData) {
      console.log('Registration successful:', userData);
    }
  }
}
</script>