<template>
  <main>
    <section class="models grid">
      <div
        v-for="item in paginatedModels"
        :key="item.id"
        @click="selectModel(item.id)"
        class="model"
        :class="item.subscription"
      >
        <img :src="item.img" :alt="item.name" class="model_img">
        <div class="model_info">
          <h2 class="model_title">{{ item.name }}</h2>
          <p class="model_age">{{ item.age }} anos</p>
          <div class="carrer">
            <span v-for="career in item.carrer[0]" :key="career">{{ career }}</span>
          </div>
        </div>
      </div>
    </section>

    <!-- Paginação -->
    <div class="pagination">
      <button
        @click="changePage(-1)"
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
        @click="changePage(1)"
        :disabled="currentPage === totalPages"
        class="pagination-button"
      >
        Próxima &raquo;
      </button>
    </div>
  </main>
</template>

<script>
import '@/assets/css/catalog_cards.css'
import '@/assets/css/global.css'

export default {
  name: 'ModelGallery',
  emits: ['models-loaded', 'model-selected'],
  
  data() {
    return {
      models: [],
      isLoading: false,
      error: null,
      currentPage: 1,
      modelsPerPage: 10
    }
  },
  
  computed: {
    totalPages() {
      return Math.ceil(this.models.length / this.modelsPerPage)
    },
    
    paginatedModels() {
      const start = (this.currentPage - 1) * this.modelsPerPage
      return this.models.slice(start, start + this.modelsPerPage)
    }
  },
  
  created() {
    this.fetchModels()
  },
  
  methods: {
    changePage(direction) {
      this.goToPage(this.currentPage + direction)
    },
    
    goToPage(page) {
      if (page >= 1 && page <= this.totalPages) {
        this.currentPage = page
        this.scrollToTop()
      }
    },
    
    scrollToTop() {
      window.scrollTo({ top: 0, behavior: 'smooth' })
    },
    
    async fetchModels() {
      this.isLoading = true
      this.error = null
      
      try {
        const response = await fetch('./api/models.json')
        if (!response.ok) throw new Error('Failed to fetch models')
        
        this.models = await response.json()
        this.$emit('models-loaded', this.models.length)
      } catch (error) {
        this.error = error.message
        console.error('Error fetching models:', error)
      } finally {
        this.isLoading = false
      }
    },
    
    selectModel(id) {
      console.log('Modelo selecionado com ID:', id)
      this.$emit('model-selected', id)
    }
  }
}
</script>