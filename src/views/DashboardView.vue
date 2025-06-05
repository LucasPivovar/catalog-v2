<template>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&family=Roboto:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
  <div class="main">
    <div class="dashboard-header">
      <h1><strong>Aura</strong>Pulse</h1>
      <div class="buttons">
        <a href="#"
           @click.prevent="activate_button('profile')"
           :class="['button', active_tab === 'profile' ? 'button-active' : '']">
          <img src="../assets/icons/white_people.svg" alt=""> Perfil
        </a>

        <a href="#"
           @click.prevent="activate_button('dashboard')"
           :class="['button', active_tab === 'dashboard' ? 'button-active' : '']">
          Dashboard
        </a>

        <a href="#"
           @click.prevent="activate_button('boost')"
           :class="['button', active_tab === 'boost' ? 'button-active' : '']">
          Boost
        </a>
      </div> 
    </div>
    
    <div class="content">
      <component :is="currentComponent" />
    </div>
  </div>
</template>

<script>
import ProfileView from '@/components/dashboard/ProfileContent.vue';
import DashboardContent from '@/components/dashboard/DashboardContent.vue';
import BoostContent from '@/components/dashboard/BoostContent.vue';

export default {
  name: 'DashboardView',
  components: {
    ProfileView,
    DashboardContent,
    BoostContent
  },
  data() {
    return {
      active_tab: 'profile',
    }
  },
  computed: {
    currentComponent() {
      switch(this.active_tab) {
        case 'profile':
          return ProfileView;
        case 'dashboard':
          return DashboardContent;
        case 'boost':
          return BoostContent;
        default:
          return ProfileView;
      }
    }
  },
  methods: {
    activate_button(tab) {
      this.active_tab = tab;
    },
    button_activated(tab) {
      return this.active_tab === tab;
    }
  }
}
</script>

<style scoped>
  .main{
    display: flex;
    flex-direction: column;
    align-items: center;
    background: var(--bg-main);
    width: 100%;
    min-height: 100vh;
  }

  .content{
    width: 100%;
    min-height: 100vh;
    max-width: 1400px;
    background: var(--bg-main);
    padding-top: 20px;
  }

  .dashboard-header{
    display: flex;
    flex-direction: row;
    color: var(--text-primary);
    align-items: center;
    justify-content: space-between;
    padding: 0 35px;
    width: 100%;
    max-width: 1400px;
    height: 80px;
  }

  .dashboard-header h1{
    font-weight: 400;
    font-size: 1.6rem;
  }

  .dashboard-header strong{
    font-weight: 400;
    color: var(--primary);
  }
  
  .buttons {
    overflow: hidden;
    padding: 8px;
    gap: 10px;
    background-color: var(--btn-secondary);
    margin: auto 0 auto 0;
    width: 100%;
    max-width: 300px;
    display: flex;
    border-radius: 5px;
  }

  .button {
    color: var(--text-secondary);
    height: 40px;
    text-align: center;
    text-decoration: none;
    transition: all 0.3s ease;
    font-weight: 500;
    border-radius: 5px;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 120px;
    padding: 0 10px;
  }

  .button-active {
    background-color: var(--btn-primary);
    color: var(--text-primary);
  }

  .button img {
    width: 10px;
    margin-right: 5px;
  }

  @media screen and (max-width: 768px) {
    .dashboard-header {
      flex-direction: column;
      align-items: center;
      padding: 0 20px;
      height: auto;
      gap: 20px;
      margin-bottom: 20px;
      margin-top: 20px;
      justify-content: center;
      text-align: center;
    }

    .button{
      width: 100%;
    }
    
  }
</style>