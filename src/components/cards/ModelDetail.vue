<template>
  <body>
    <main>
        <div class="back-button-container">
          <button @click="goBack" class="back-button">
            ← Voltar para a lista
          </button>
        </div>
        
        <div v-if="isLoading" class="loading">Carregando...</div>        
        <div v-else-if="error" class="error">Erro: {{ error }}</div>
        
        <div class="sidebox" v-else-if="model">
          <h1 class="model-title">{{ model.name }}</h1>
          <div class="content">
            <!-- Gallery Box -->
            <div class="box gallery-box">
              <img v-if="currentImage" :src="getImageUrl(currentImage)" alt="model" class="main-image">
              <div v-else class="no-image">Imagem não disponível</div>
              
              <div class="gallery-footer" v-if="images.length">
                <img 
                  v-for="(img, index) in images" 
                  :key="index"
                  :src="getImageUrl(img)" 
                  alt="model" 
                  class="img-preview" 
                  @click="changeImage(img)"
                  :class="{ active: currentImage === img }"
                >
              </div>
            </div>
            <div class="box details-box">
              <div class="model-main">
                <div class="contact-links" v-if="hasContacts">
                  <a 
                    v-for="(contact, type) in contacts" 
                    :key="type"
                    :href="contact" 
                    target="_blank" 
                    :class="['contact-link', type]"
                  >
                    {{ formatContactType(type) }}
                  </a>
                </div>
                <div class="review-section" v-if="hasComments">
                  <h3>Review</h3>
                  <div class="overall-rating">
                    <div class="stars" v-html="getStarsHtml(averageRating)"></div>
                    <div class="rating-text">{{ averageRating.toFixed(1) }}/5 ({{ model.comments.length }} avaliações)</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="reviews-container" v-if="hasComments">
          <h3 class="reviews-title">Avaliações</h3>
          <div class="reviews-list">
            <div 
              v-for="comment in model.comments" 
              :key="comment.id" 
              class="review-item"
            >
              <div class="review-stars" v-html="getStarsHtml(comment.stars)"></div>
              <p class="review-text">{{ comment.comment }}</p>
            </div>
          </div>
        </div>
    </main>
  </body>
</template>

<script>
import '@/assets/css/global.css'
import '@/assets/css/model_detail.css'

export default {
  name: 'ModelDetail',

  props: {
    modelId: {
      type: [String, Number],
      required: true
    }
  },

  emits: ['back-to-list'],
  
  data() {
    return {
      model: null,
      currentImage: null,
      isLoading: false,
      error: null,
      apiPaths: [
        `/api/models/{id}/dados.json`,
        `./api/models/{id}/dados.json`,
        `/public/api/models/{id}/dados.json`
      ]
    }
  },

  computed: {
    images() {
      const imageData = this.model?.images?.[0]
      if (!imageData) return []
      
      return [imageData.imagem1, imageData.imagem2, imageData.imagem3].filter(Boolean)
    },

    contacts() {
      const contactData = this.model?.contact?.[0]
      if (!contactData) return {}
      
      return Object.fromEntries(
        Object.entries(contactData).filter(([, value]) => value)
      )
    },

    hasContacts() {
      return Object.keys(this.contacts).length > 0
    },

    hasComments() {
      return this.model?.comments?.length > 0
    },

    averageRating() {
      if (!this.hasComments) return 0
      
      const totalStars = this.model.comments.reduce((sum, comment) => sum + comment.stars, 0)
      return totalStars / this.model.comments.length
    }
  },

  watch: {
    modelId: {
      handler: 'fetchModel',
      immediate: true
    }
  },

  methods: {
    async fetchModel() {
      if (!this.modelId) {
        this.setError('ID do modelo não fornecido')
        return
      }

      this.setLoadingState(true)
      
      for (const pathTemplate of this.apiPaths) {
        const path = pathTemplate.replace('{id}', this.modelId)
        
        try {
          const response = await fetch(path)
          if (response.ok) {
            const data = await response.json()
            this.setModelData(data)
            return
          }
        } catch (error) {
          continue
        }
      }
      
      this.setError('Modelo não encontrado')
    },

    setLoadingState(loading) {
      this.isLoading = loading
      if (loading) {
        this.error = null
      }
    },

    setError(message) {
      this.error = message
      this.isLoading = false
    },

    setModelData(data) {
      this.model = data
      this.currentImage = this.images[0] || null
      this.isLoading = false
    },
    
    changeImage(imageUrl) {
      this.currentImage = imageUrl
    },

    getImageUrl(imagePath) {
      if (!imagePath || imagePath.startsWith('http')) return imagePath || ''
      
      const cleanPath = imagePath.replace(/^\.\//, '')
      return cleanPath.startsWith('/') ? cleanPath : '/' + cleanPath
    },

    formatContactType(type) {
      return type.charAt(0).toUpperCase() + type.slice(1)
    },

    goBack() {
      this.$emit('back-to-list')
    },

    getStarsHtml(rating) {
      return Array.from({ length: 5 }, (_, i) => {
        const starClass = i + 1 <= rating ? 'star filled' : 'star'
        return `<div class="${starClass}">★</div>`
      }).join('')
    }
  }
}
</script>