<template>
  <div class="main">
    <section class="models grid">
       <div 
         v-for="item in paginatedModels"
         :key="item.id" 
         @click="navigateToModel(item.id)"
         class="model"
         :class="item.subscription"
       >
         <img :src="item.img" :alt="item.name" class="model_img">
         <div class="model_info">
           <h2 class="model_title">{{ item.name }}</h2>
           <p class="model_age">{{ item.age }} anos</p>
           <div class="carrer">
            <span>{{ item.carrer[0].carrer1 }}</span>
            <span>{{ item.carrer[0].carrer2 }}</span>
           </div>
         </div>
       </div>
    </section>

    <!-- Paginação -->
    <div class="pagination">
      <button 
        @click="goToPage(currentPage - 1)" 
        :disabled="currentPage === 1"
        class="pagination-button"
      >
        &laquo; Anterior
      </button>
      
      <div class="page-numbers">
        <button 
          v-for="page in totalPages" 
          :key="page" 
          @click="goToPage(page)"
          class="page-number"
          :class="{ active: page === currentPage }"
        >
          {{ page }}
        </button>
      </div>
      
      <button 
        @click="goToPage(currentPage + 1)" 
        :disabled="currentPage === totalPages"
        class="pagination-button"
      >
        Próxima &raquo;
      </button>
    </div>
  </div>
</template>

<script>
import '@/assets/css/catalog_cards.css';
import '@/assets/css/global.css';

export default {
  name: 'ModelGallery',
  emits: ['models-loaded'],

  data() {
    return {
      models: [],
      isLoading: false,
      error: null,
      // Paginação
      currentPage: 1,
      modelsPerPage: 10
    }
  },

  created() {
    this.fetchModels()
  },
  
  computed: {
    // Calcular total de páginas
    totalPages() {
      return Math.ceil(this.models.length / this.modelsPerPage)
    },
    
    // Filtrar modelos para a página atual
    paginatedModels() {
      const start = (this.currentPage - 1) * this.modelsPerPage
      const end = start + this.modelsPerPage
      return this.models.slice(start, end)
    }
  },

  methods: {
    goToPage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page
        window.scrollTo({ top: 0, behavior: 'smooth' })
      }
    },
    
    fetchModels() {
      this.isLoading = true;
      this.error = null;
      
      fetch('./api/models.json')
        .then(response => {
          if (!response.ok) {
            throw new Error('Failed to fetch models');
          }
          return response.json();
        })
        .then(data => {
          this.models = data;
          this.isLoading = false;
          this.$emit('models-loaded', this.models.length);
        })
        .catch(error => {
          this.error = error.message;
          this.isLoading = false;
          console.error('Error fetching models:', error);
        });
    },

    navigateToModel(id) {
      // Debug para verificar se o ID está sendo passado corretamente
      console.log('Navegando para modelo com ID:', id);
      
      // Navegar para a página de detalhes do modelo
      this.$router.push(`/model/${id}`);
    },

    getModelsCount() {
      return this.models.length;
    }
  }
}
</script>