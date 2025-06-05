<template>   
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:ital,opsz,wght@0,14..32,100..900;1,14..32,100..900&family=Libre+Baskerville:ital,wght@0,400;0,700;1,400&family=Roboto:ital,wght@0,100..900;1,100..900&display=swap" rel="stylesheet">
  <body>     
    <div class="main">   
      <div class="content_home">    
        <app-header :models-count="modelsCount" />
        
        <!-- Mostrar lista de modelos ou detalhes baseado no estado -->
        <catalog-cards          
          v-if="!selectedModelId"
          ref="catalogCardsRef"         
          @models-loaded="updateModelsCount"
          @model-selected="showModelDetails"
        />
        
        <!-- Componente de detalhes do modelo -->
        <model-detail
          v-if="selectedModelId"
          :model-id="selectedModelId"
          @back-to-list="backToList"
        />
      </div>
    </div>      
  </body> 
  <a href="/dashboard" class="dashboard">Dashboard</a>
</template>  

<script>
import AppHeader from '@/components/AppHeader.vue';
import catalogCards from '@/components/cards/ModelCards.vue';
import ModelDetail from '@/components/cards/ModelDetail.vue';
import '@/assets/css/home_view.css';
import '@/assets/css/global.css';

export default {   
  name: 'HomeView',   
  components: {     
    catalogCards,
    ModelDetail,
    AppHeader,
  },      
  
  data() {     
    return {       
      modelsCount: 0,
      selectedModelId: null
    }   
  },      
  
  mounted() {     
    this.$nextTick(() => {       
      this.checkModelsCount();     
    });   
  },      
  
  methods: {     
    updateModelsCount(count) {       
      this.modelsCount = count;       
      console.log('Quantidade atualizada:', count);     
    },          
    
    checkModelsCount() {       
      if (this.$refs.catalogCardsRef) {         
        const count = this.$refs.catalogCardsRef.getModelsCount();         
        if (count > 0) {           
          this.modelsCount = count;         
        } else {           
          setTimeout(() => {             
            this.checkModelsCount();           
          }, 500);         
        }       
      } else {         
        setTimeout(() => {           
          this.checkModelsCount();         
        }, 200);       
      }     
    },

    showModelDetails(modelId) {
      this.selectedModelId = modelId;
      // Scroll para o topo quando mostrar detalhes
      window.scrollTo({ top: 0, behavior: 'smooth' });
    },

    backToList() {
      this.selectedModelId = null;
      // Scroll para o topo quando voltar para a lista
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }
  } 
} 
</script>  

<style scoped>
.main {
  font-family: "Inter", sans-serif;   
  background: var(--bg-main);
  min-height: 100vh;
}    

.content_home {     
  width: 100%;     
  min-height: 100vh;     
  max-width: 1400px;
  margin: 0 auto;
}

.dashboard {
  position: fixed;
  bottom: 20px;
  right: 20px;
  color: var(--text-primary);
  text-decoration: none;
  background: var(--primary);
  padding: 10px 20px;
  border-radius: 20px;
  font-weight: 500;
  transition: all 0.3s ease;
}

.dashboard:hover {
  opacity: 0.8;
  transform: translateY(-2px);
}
</style>