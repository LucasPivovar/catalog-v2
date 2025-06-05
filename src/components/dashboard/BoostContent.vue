<template>
  <main>
    <div class="boost-container">
      <h1 class="section-title">Impulsionamento</h1>
      <div class="boost-content">
        <div class="section-header">
          <h2>Seus Créditos</h2>
          <p class="subtitle">Gerencie seus créditos de impulsionamento</p>
        </div>
        
        <div class="credits-display">
          <div class="credit-amount">{{ totalCredits }}</div>
          <div class="credit-label">créditos disponíveis</div>
        </div>
      
        <div class="section-header">
          <h2>Comprar Mais Créditos</h2>
        </div>
      
        <div class="credit-options">
          <div 
            v-for="option in creditOptions" 
            :key="option.amount"
            class="credit-option" 
            :class="{ 'selected': selectedOption === option.amount, 'popular': option.popular }"
            @click="selectCreditOption(option.amount, option.price)"
          >
            <div v-if="option.popular" class="popular-tag">Popular</div>
            <div class="option-amount">{{ option.amount }}</div>
            <div class="option-label">créditos</div>
            <div class="option-price">R$ {{ option.price.toFixed(2) }}</div>
          </div>
        </div>
      
        <button class="action-button" @click="buyCredits">
          Comprar Créditos
        </button>
      </div>
    
      <div class="boost-content">
        <div class="section-header">
          <h2>Impulsionar Perfil</h2>
          <p class="subtitle">Escolha como deseja impulsionar seu perfil</p>
        </div>
        
        <div class="tab-buttons">
          <button 
            v-for="tab in tabs" 
            :key="tab.id"
            class="tab-button"
            :class="{ 'active': activeTab === tab.id }"
            @click="activeTab = tab.id"
          >
            {{ tab.name }}
          </button>
        </div>
        
        <div v-if="activeTab === 'schedule'" class="tab-content">
          <div class="schedule-fields">
            <div class="field">
              <label>Data de Início</label>
              <div class="date-picker clickable" @click="focusDateInput">
                <input 
                  ref="dateInput"
                  type="date" 
                  v-model="scheduleDate" 
                  class="date-field"
                />
              </div>
            </div>
            
            <div class="field">
              <label>Horário</label>
              <div class="time-input clickable" @click="focusTimeInput">
                <input 
                  ref="timeInput"
                  type="time" 
                  v-model="scheduleTime"
                  class="time-field"
                />
              </div>
            </div>
          </div>
          
          <div class="cost-info">
            <span class="cost-label">Custo:</span>
            <span class="cost-value">{{ boostCost }} créditos</span>
          </div>
          
          <p class="schedule-info">Impulsionamento programado por 24 horas na data selecionada.</p>
          
          <button class="action-button" @click="scheduleBoost">

            Agendar Impulsão
          </button>
        </div>
        
        <div v-if="activeTab === 'now'" class="tab-content">
          <div class="now-boost-content">
            <p class="boost-description">
              Seu perfil será impulsionado imediatamente por 24 horas após a confirmação.
            </p>
            <div class="cost-info">
              <span class="cost-label">Custo:</span>
              <span class="cost-value">{{ boostCost }} créditos</span>
            </div>
            <button class="action-button" @click="boostNow">
              Impulsionar Agora
            </button>
          </div>
        </div>
        
        <div v-if="showAlert" class="alert-container">
          <div class="alert">
            <span class="alert-message">{{ alertMessage }}</span>
            <button class="alert-close" @click="showAlert = false">×</button>
          </div>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import '@/assets/css/boost_content.css';
import '@/assets/css/global.css';

export default {
  name: 'BoostContent',
  data() {
    return {
      totalCredits: 250,
      selectedOption: 1000,
      selectedPrice: 159.90,
      boostCost: 100,
      
      activeTab: 'schedule',
      scheduleDate: '',
      scheduleTime: '00:00',
      showAlert: false,
      alertMessage: '',
      
      creditOptions: [
        { amount: 100, price: 19.90, popular: false },
        { amount: 500, price: 89.90, popular: true },
        { amount: 1000, price: 159.90, popular: false }
      ],
      tabs: [
        { id: 'now', name: 'Impulsionar Agora' },
        { id: 'schedule', name: 'Agendar Impulsão' }
      ]
    }
  },
  methods: {
    selectCreditOption(amount, price) {
      this.selectedOption = amount;
      this.selectedPrice = price;
    },
    buyCredits() {
      if (!this.selectedOption) {
        this.showAlertMessage('Por favor, selecione uma opção de créditos.');
        return;
      }
      
      setTimeout(() => {
        this.totalCredits += this.selectedOption;
        this.showAlertMessage(`${this.selectedOption} créditos adicionados com sucesso!`);
      }, 500);
    },
    
    focusDateInput() {
      this.$refs.dateInput.focus();
      this.$refs.dateInput.showPicker && this.$refs.dateInput.showPicker();
    },
    
    focusTimeInput() {
      this.$refs.timeInput.focus();
      this.$refs.timeInput.showPicker && this.$refs.timeInput.showPicker();
    },
    
    scheduleBoost() {
      if (this.totalCredits < this.boostCost) {
        this.showAlertMessage('Créditos insuficientes para agendar impulsionamento.');
        return;
      }
      
      if (!this.scheduleDate) {
        this.showAlertMessage('Por favor, selecione uma data para o agendamento.');
        return;
      }
      
      setTimeout(() => {
        this.totalCredits -= this.boostCost;
        this.showAlertMessage(`Impulsionamento agendado para ${this.formatDisplayDate(this.scheduleDate)} às ${this.scheduleTime}!`);
      }, 500);
    },
    
    boostNow() {
      if (this.totalCredits < this.boostCost) {
        this.showAlertMessage('Créditos insuficientes para impulsionar perfil.');
        return;
      }
      
      setTimeout(() => {
        this.totalCredits -= this.boostCost;
        this.showAlertMessage('Perfil está sendo impulsionado por 24 horas!');
      }, 500);
    },
    
    formatDisplayDate(dateString) {
      if (!dateString) return '';
      
      const date = new Date(dateString);
      const day = date.getDate().toString().padStart(2, '0');
      const month = (date.getMonth() + 1).toString().padStart(2, '0');
      const year = date.getFullYear();
      
      return `${day}/${month}/${year}`;
    },
    
    showAlertMessage(message) {
      this.alertMessage = message;
      this.showAlert = true;
      
      setTimeout(() => {
        this.showAlert = false;
      }, 5000);
    }
  }
}
</script>
