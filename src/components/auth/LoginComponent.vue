<template>
  <div class="login">
    <div class="form-group">
      <label for="email">Email</label>
      <div class="input-container">
        <div class="icon-left">
          <img src="@/assets/icons/Mail.svg" alt="Email">
        </div>
        <input 
          type="email" 
          id="email" 
          class="input-field" 
          placeholder="seu@email.com" 
          v-model="loginForm.email">
      </div>
    </div>
    
    <div class="form-group">
      <label for="password">Senha</label>
      <div class="input-container">
        <div class="icon-left">
          <img src="@/assets/icons/Lock.svg" alt="Password">
        </div>
        <input 
          :type="showPassword ? 'text' : 'password'" 
          id="password" 
          class="input-field" 
          placeholder="••••••••" 
          v-model="loginForm.password"
          >
        <div class="icon-right">
          <img src="@/assets/icons/Eye.svg" alt="Show password" @click="showPassword = !showPassword" class="eye-icon" v-if="!showPassword">
          <img src="@/assets/icons/EyeOff.svg" alt="Hide password" @click="showPassword = !showPassword" class="eye-icon" v-else>

        </div>
      </div>
    </div>
    
    <div class="remember-forgot">
      <div class="remember-me">
        <input type="checkbox" id="remember" v-model="loginForm.remember">
        <label for="remember">Lembrar de mim</label>
      </div>
      <a href="#" class="forgot-password" @click.prevent="handleForgotPassword">Esqueceu a senha?</a>
    </div>
    
    <button class="submit-button" @click="login" :disabled="isLoading">
      {{ isLoading ? 'Entrando...' : 'Entrar' }}
    </button>
    
    <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
  </div>
</template>

<script>
export default {
  name: 'LoginComponent',
  data() {
    return {
      showPassword: false,
      isLoading: false,
      errorMessage: '',
      loginForm: {
        email: '',
        password: '',
        remember: false
      }
    }
  },
  methods: {
    togglePassword() {
      this.showPassword = !this.showPassword;
    },
    async login() {
      if (!this.validateForm()) return;
      
      this.isLoading = true;
      this.errorMessage = '';
      
      try {
        await new Promise(resolve => setTimeout(resolve, 1000));
        
        console.log('Login attempt:', this.loginForm);
        
        this.$emit('login-success', {
          email: this.loginForm.email,
          remember: this.loginForm.remember
        });
        
      } catch (error) {
        this.errorMessage = 'Erro ao fazer login. Verifique suas credenciais.';
        console.error('Login error:', error);
      } finally {
        this.isLoading = false;
      }
    },
    validateForm() {
      if (!this.loginForm.email) {
        this.errorMessage = 'Por favor, insira seu email.';
        return false;
      }
      if (!this.loginForm.password) {
        this.errorMessage = 'Por favor, insira sua senha.';
        return false;
      }
      if (!this.isValidEmail(this.loginForm.email)) {
        this.errorMessage = 'Por favor, insira um email válido.';
        return false;
      }
      return true;
    },
    isValidEmail(email) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return emailRegex.test(email);
    },
    handleForgotPassword() {
      console.log('Forgot password clicked');
    },
    switchToRegister() {
      this.$emit('switch-tab', 'register');
    }
  }
}
</script>