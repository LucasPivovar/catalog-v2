<template>
  <div class="register">
    <div class="form-group">
      <label for="fullName">Nome Completo</label>
      <div class="input-container">
        <div class="icon-left">
          <img src="@/assets/icons/User.svg" alt="User">
        </div>
        <input 
          type="text" 
          id="fullName" 
          class="input-field" 
          placeholder="Seu nome completo" 
          v-model="registerForm.fullName">
      </div>
    </div>
    
    <div class="form-group">
      <label for="regEmail">Email</label>
      <div class="input-container">
        <div class="icon-left">
          <img src="@/assets/icons/Mail.svg" alt="Email">
        </div>
        <input 
          type="email" 
          id="regEmail" 
          class="input-field" 
          placeholder="seu@email.com" 
          v-model="registerForm.email">
      </div>
    </div>
    
    <div class="form-group">
      <label for="cpf">CPF</label>
      <div class="input-container">
        <div class="icon-left">
        </div>
        <input 
          type="text" 
          inputmode="numeric"
          id="cpf" 
          class="input-field" 
          placeholder="000.000.000-00" 
          v-model="formattedCPF"
          @input="handleCPFInput">
      </div>
    </div>
    
    <div class="form-group">
      <label for="birthdate">Data de Nascimento</label>
      <div class="input-container date-container" @click="openDatePicker">
        <div class="icon-left">
          <img src="@/assets/icons/Calendar.svg" alt="Calendar">
        </div>
        <input 
          type="text" 
          class="input-field date-display" 
          placeholder="DD/MM/AAAA" 
          :value="displayBirthdate"
          readonly>
        <div class="icon-right">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="calendar-icon">
            <rect x="3" y="4" width="18" height="18" rx="2" ry="2"></rect>
            <line x1="16" y1="2" x2="16" y2="6"></line>
            <line x1="8" y1="2" x2="8" y2="6"></line>
            <line x1="3" y1="10" x2="21" y2="10"></line>
          </svg>
        </div>
        <input 
          type="date" 
          ref="birthdateInput"
          v-model="registerForm.birthdate"
          class="hidden-date-input"
          @change="formatDate">
      </div>
    </div>

    <div class="form-group">
      <label>Fotos do RG</label>
      <div class="photo-preview-container">
        <div v-for="side in rgSides" :key="side.key" class="photo-upload-section">
          <label class="photo-label">{{ side.label }}</label>
          <div class="photo-preview" v-if="registerForm[side.photoKey]">
            <img :src="registerForm[side.photoKey]" :alt="side.label" class="preview-image">
            <div class="photo-overlay" @click="removePhoto(side.key)">
              <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="remove-icon">
                <circle cx="12" cy="12" r="10"></circle>
                <line x1="15" y1="9" x2="9" y2="15"></line>
                <line x1="9" y1="9" x2="15" y2="15"></line>
              </svg>
            </div>
          </div>
          <div v-else class="add-photo" @click="triggerFileInput(side.key)">
            <div class="add-icon">📷</div>
            <div class="add-text">{{ side.label }}</div>
          </div>
          <input 
            type="file" 
            :ref="side.inputRef"
            class="photo-input" 
            accept="image/*" 
            @change="handlePhotoUpload($event, side.key)">
        </div>
      </div>
      <p v-if="photoUploadError" class="error-message">{{ photoUploadError }}</p>
    </div>
    
    <div class="form-group">
      <label for="regPassword">Senha</label>
      <div class="input-container">
        <div class="icon-left">
          <img src="@/assets/icons/Lock.svg" alt="Password">
        </div>
        <input 
          :type="showPassword ? 'text' : 'password'" 
          id="regPassword" 
          class="input-field" 
          placeholder="••••••••" 
          v-model="registerForm.password">
        <div class="icon-right password-toggle" @click="togglePassword">
          <img src="@/assets/icons/Eye.svg" alt="Show Password" v-if="showPassword" class="eye-icon">
          <img src="@/assets/icons/EyeOff.svg" alt="Hide Password" v-else class="eye-icon">
        </div>
      </div>
    </div>  
    
    <div class="form-group">
      <label for="confirmPassword">Confirmar Senha</label>
      <div class="input-container">
        <div class="icon-left">
          <img src="@/assets/icons/Lock.svg" alt="Confirm Password">
        </div>
        <input
          :type="showConfirmPassword ? 'text' : 'password'" 
          id="confirmPassword" 
          class="input-field" 
          placeholder="••••••••" 
          v-model="registerForm.confirmPassword">
        <div class="icon-right password-toggle" @click="toggleConfirmPassword">
          <img src="@/assets/icons/Eye.svg" alt="Show Password" v-if="showConfirmPassword" class="eye-icon">
          <img src="@/assets/icons/EyeOff.svg" alt="Hide Password" v-else class="eye-icon">
        </div>
      </div>
      <p v-if="passwordMismatch" class="error-message">As senhas não coincidem</p>
    </div>
    
    <div class="remember-forgot">
      <div class="accept-terms">
        <input type="checkbox" id="terms" v-model="registerForm.acceptTerms">
        <label for="terms">
          Eu aceito os <a href="#" @click.prevent="openModal('terms')" class="terms-link">Termos de Uso</a> e 
          <a href="#" @click.prevent="openModal('privacy')" class="terms-link">Política de Privacidade</a>
        </label>
      </div>
    </div>
    
    <button class="submit-button" @click="register" :disabled="isLoading">
      {{ isLoading ? 'Criando Conta...' : 'Criar Conta' }}
    </button>
    
    <p v-if="errorMessage" class="error-message">{{ errorMessage }}</p>
    
    <div v-if="showModal" class="modal-overlay" @click="closeModal">
      <div class="modal-content" @click.stop>
        <div class="modal-header">
          <h2>{{ modalTitle }}</h2>
          <button class="modal-close" @click="closeModal">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <line x1="18" y1="6" x2="6" y2="18"></line>
              <line x1="6" y1="6" x2="18" y2="18"></line>
            </svg>
          </button>
        </div>
        <div class="modal-body">
          <div v-html="modalContent"></div>
        </div>
        <div class="modal-footer">
          <button class="modal-button" @click="closeModal">Fechar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RegisterComponent',
  data() {
    return {
      showPassword: false,
      showConfirmPassword: false,
      showModal: false,
      modalType: '',
      photoUploadError: '',
      errorMessage: '',
      isLoading: false,
      registerForm: {
        fullName: '',
        email: '',
        cpf: '',
        birthdate: '',
        password: '',
        confirmPassword: '',
        acceptTerms: false,
        rgFrontPhoto: null,
        rgBackPhoto: null
      },
      formattedCPF: '',
      displayBirthdate: '',
      rgSides: [
        { key: 'front', label: 'Frente do RG', photoKey: 'rgFrontPhoto', inputRef: 'rgFrontInput' },
        { key: 'back', label: 'Verso do RG', photoKey: 'rgBackPhoto', inputRef: 'rgBackInput' }
      ]
    }
  },
  computed: {
    passwordMismatch() {
      if (!this.registerForm.confirmPassword) return false;
      return this.registerForm.password !== this.registerForm.confirmPassword;
    },
    modalTitle() {
      return this.modalType === 'terms' ? 'Termos de Uso' : 'Política de Privacidade';
    },
    modalContent() {
      if (this.modalType === 'terms') {
        return `
          <h3>1. Aceitação dos Termos</h3>
          <p>Ao utilizar nossos serviços, você concorda com estes termos de uso.</p>
          <h3>2. Uso do Serviço</h3>
          <p>Você concorda em usar nosso serviço apenas para fins legais e de acordo com estes termos.</p>
          <h3>3. Conta do Usuário</h3>
          <p>Você é responsável por manter a confidencialidade de sua conta e senha.</p>
          <h3>4. Privacidade</h3>
          <p>Respeitamos sua privacidade conforme descrito em nossa Política de Privacidade.</p>
          <h3>5. Limitação de Responsabilidade</h3>
          <p>Nosso serviço é fornecido "como está" sem garantias de qualquer tipo.</p>
          <h3>6. Modificações</h3>
          <p>Podemos modificar estes termos a qualquer momento. As alterações entrarão em vigor imediatamente.</p>
          <h3>7. Contato</h3>
          <p>Para dúvidas sobre estes termos, entre em contato conosco através do e-mail: contato@crimsonauth.com</p>
        `;
      } else {
        return `
          <h3>1. Coleta de Informações</h3>
          <p>Coletamos informações que você nos fornece diretamente, como nome, e-mail e CPF.</p>
          <h3>2. Uso das Informações</h3>
          <p>Utilizamos suas informações para fornecer e melhorar nossos serviços.</p>
          <h3>3. Compartilhamento de Dados</h3>
          <p>Não vendemos, comercializamos ou transferimos suas informações pessoais para terceiros.</p>
          <h3>4. Segurança</h3>
          <p>Implementamos medidas de segurança para proteger suas informações pessoais.</p>
          <h3>5. Cookies</h3>
          <p>Utilizamos cookies para melhorar sua experiência em nosso site.</p>
          <h3>6. Seus Direitos</h3>
          <p>Você tem o direito de acessar, corrigir ou excluir suas informações pessoais.</p>
          <h3>7. Contato</h3>
          <p>Para questões sobre privacidade, entre em contato: privacidade@crimsonauth.com</p>
        `;
      }
    }
  },
  methods: {
    togglePassword() {
      this.showPassword = !this.showPassword;
    },
    toggleConfirmPassword() {
      this.showConfirmPassword = !this.showConfirmPassword;
    },

    isValidCPF(cpf) {
      cpf = cpf.replace(/\D/g, '');
      
      if (cpf.length !== 11) return false;

      if (/^(\d)\1{10}$/.test(cpf)) return false;
      
      let sum = 0;
      for (let i = 0; i < 9; i++) {
        sum += parseInt(cpf.charAt(i)) * (10 - i);
      }
      let remainder = (sum * 10) % 11;
      if (remainder === 10 || remainder === 11) remainder = 0;
      if (remainder !== parseInt(cpf.charAt(9))) return false;
      
      sum = 0;
      for (let i = 0; i < 10; i++) {
        sum += parseInt(cpf.charAt(i)) * (11 - i);
      }
      remainder = (sum * 10) % 11;
      if (remainder === 10 || remainder === 11) remainder = 0;
      if (remainder !== parseInt(cpf.charAt(10))) return false;
      
      return true;
    },
    
    isValidAge(birthdate) {
      const today = new Date();
      const birth = new Date(birthdate);
      const age = today.getFullYear() - birth.getFullYear();
      const monthDiff = today.getMonth() - birth.getMonth();
      
      if (monthDiff < 0 || (monthDiff === 0 && today.getDate() < birth.getDate())) {
        return age - 1 >= 18;
      }
      return age >= 18;
    },

    async register() {
      if (!this.validateForm()) return;
      
      this.isLoading = true;
      this.errorMessage = '';
      
      try {
        await new Promise(resolve => setTimeout(resolve, 2000));
        
        console.log('Register attempt:', this.registerForm);
        
        this.$emit('register-success', {
          fullName: this.registerForm.fullName,
          email: this.registerForm.email,
          cpf: this.registerForm.cpf
        });
        
      } catch (error) {
        this.errorMessage = 'Erro ao criar conta. Tente novamente.';
        console.error('Registration error:', error);
      } finally {
        this.isLoading = false;
      }
    },

    isValidEmail(email) {
      const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
      return emailRegex.test(email);
    },
    
    handleCPFInput(event) {
      const rawValue = event.target.value.replace(/\D/g, '');
      this.registerForm.cpf = rawValue.substring(0, 11);
      
      if (rawValue.length <= 3) {
        this.formattedCPF = rawValue;
      } else if (rawValue.length <= 6) {
        this.formattedCPF = rawValue.substring(0, 3) + '.' + rawValue.substring(3);
      } else if (rawValue.length <= 9) {
        this.formattedCPF = rawValue.substring(0, 3) + '.' + rawValue.substring(3, 6) + '.' + rawValue.substring(6);
      } else {
        this.formattedCPF = rawValue.substring(0, 3) + '.' + rawValue.substring(3, 6) + '.' + 
                          rawValue.substring(6, 9) + '-' + rawValue.substring(9, 11);
      }
    },
    
    openDatePicker() {
      if (this.$refs.birthdateInput) {
        this.$refs.birthdateInput.click();
      }
    },
    
    formatDate() {
      if (this.registerForm.birthdate) {
        const [year, month, day] = this.registerForm.birthdate.split('-');
        this.displayBirthdate = `${day}/${month}/${year}`;
      }
    },
    
    triggerFileInput(side) {
      const inputRef = side === 'front' ? 'rgFrontInput' : 'rgBackInput';
      const input = this.$refs[inputRef];
      if (input && input[0]) {
        input[0].click();
      }
    },
    
    handlePhotoUpload(event, side) {
      const file = event.target.files[0];
      if (!file) return;
      
      if (!file.type.startsWith('image/') || file.size > 5 * 1024 * 1024) {
        this.photoUploadError = file.size > 5 * 1024 * 1024 ? 
          'A imagem deve ter no máximo 5MB' : 'Por favor, selecione apenas arquivos de imagem';
        return;
      }
      
      this.photoUploadError = '';
      
      const reader = new FileReader();
      reader.onload = (e) => {
        this.registerForm[side === 'front' ? 'rgFrontPhoto' : 'rgBackPhoto'] = e.target.result;
      };
      reader.readAsDataURL(file);
    },
    
    removePhoto(side) {
      const photoKey = side === 'front' ? 'rgFrontPhoto' : 'rgBackPhoto';
      const inputRef = side === 'front' ? 'rgFrontInput' : 'rgBackInput';
      this.registerForm[photoKey] = null;
      const input = this.$refs[inputRef];
      if (input && input[0]) {
        input[0].value = '';
      }
    },
    
    openModal(type) {
      this.modalType = type;
      this.showModal = true;
      document.body.style.overflow = 'hidden';
    },
    
    closeModal() {
      this.showModal = false;
      document.body.style.overflow = 'auto';
    },
    
    switchToLogin() {
      this.$emit('switch-tab', 'login');
    }
  },
  
  mounted() {
    if (this.$refs.birthdateInput) {
      this.$refs.birthdateInput.max = new Date().toISOString().split('T')[0];
    }
  }
}
</script>