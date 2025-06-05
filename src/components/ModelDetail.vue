<template>
  <body>
    <main>
      <container>
        <div class="back-button-container">
          <button @click="goBack" class="back-button">← Voltar</button>
        </div>        
        
        <div v-if="isLoading" class="loading">Carregando...</div>        
        <div v-else-if="error" class="error">Erro: {{ error }}</div>

        <sidebox v-else-if="model">

            <box class="gallery-box">
                <div class="header">
                    <h1 class="model-title">{{ model.name }}</h1>
                </div>
                <img v-if="currentImage" :src="getImageUrl(currentImage)" alt="model" class="main-image">
                <div v-else class="no-image">Imagem não disponível</div>
                
                <div class="gallery-footer" v-if="model.images?.[0]">
                <img 
                    v-for="(img, key) in getImages()" 
                    :key="key"
                    :src="getImageUrl(img)" 
                    alt="model" 
                    class="img-preview" 
                    @click="changeImage(img)"
                    :class="{ active: currentImage === img }"
                >
                </div>
            </box>

          <box class="details-box">
            
            <div class="model-main">
              <div class="contact-links">
                <a 
                  v-for="(contact, type) in getContacts()" 
                  :key="type"
                  :href="contact" 
                  target="_blank" 
                  :class="['contact-link', type]"
                >
                  {{ type.charAt(0).toUpperCase() + type.slice(1) }}
                </a>
              </div>
              


              <!-- Seção de Review -->
              <div class="review-section" v-if="model.comments && model.comments.length > 0">
                <h3>Review</h3>
                <div class="overall-rating">
                  <div class="stars" v-html="getStarsHtml(getAverageRating())"></div>
                  <div class="rating-text">{{ getAverageRating().toFixed(1) }}/5 ({{ model.comments.length }} avaliações)</div>
                </div>
              </div>
            </div>
          </box>
        </sidebox>

        <!-- Seção de Avaliações -->
        <div class="reviews-container" v-if="model && model.comments && model.comments.length > 0">
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
      </container>
    </main>
  </body>
</template>

<script>
import '@/assets/css/global.css';
import '@/assets/css/model_detail.css';

export default {
  name: 'ModelDetail',
  
  data() {
    return {
      model: null,
      currentImage: null,
      isLoading: false,
      error: null
    }
  },

  created() {
    this.fetchModel()
  },

  watch: {
    '$route'() {
      this.fetchModel()
    }
  },

  methods: {
    async fetchModel() {
      const id = this.$route.params.id;
      
      if (!id) {
        this.error = 'ID do modelo não fornecido';
        return;
      }

      this.isLoading = true;
      this.error = null;
      
      const paths = [
        `/api/models/${id}/dados.json`,
        `./api/models/${id}/dados.json`,
        `/public/api/models/${id}/dados.json`
      ];
      
      for (const path of paths) {
        try {
          const response = await fetch(path);
          if (response.ok) {
            const data = await response.json();
            this.model = data;
            this.currentImage = data.images?.[0]?.imagem1 || null;
            this.isLoading = false;
            return;
          }
        } catch (error) {
          continue;
        }
      }
      
      this.error = 'Modelo não encontrado';
      this.isLoading = false;
    },
    
    changeImage(imageUrl) {
      this.currentImage = imageUrl;
    },

    getImageUrl(imagePath) {
      if (!imagePath || imagePath.startsWith('http')) return imagePath || '';
      
      const cleanPath = imagePath.replace(/^\.\//, '');
      return cleanPath.startsWith('/') ? cleanPath : '/' + cleanPath;
    },

    getImages() {
      const images = this.model.images?.[0];
      if (!images) return [];
      
      return [images.imagem1, images.imagem2, images.imagem3].filter(Boolean);
    },

    getContacts() {
      const contact = this.model.contact?.[0];
      if (!contact) return {};
      
      return Object.fromEntries(
        Object.entries(contact).filter(([, value]) => value)
      );
    },

    goBack() {
      if (window.history.length > 1) {
        this.$router.go(-1);
      } else {
        this.$router.push('/');
      }
    },

    getAverageRating() {
      if (!this.model.comments || this.model.comments.length === 0) {
        return 0;
      }
      
      const totalStars = this.model.comments.reduce((sum, comment) => sum + comment.stars, 0);
      return totalStars / this.model.comments.length;
    },

    getStarsHtml(rating) {
      let starsHtml = '';
      for (let i = 1; i <= 5; i++) {
        const starClass = i <= rating ? 'star filled' : 'star';
        starsHtml += `<div class="${starClass}">★</div>`;
      }
      return starsHtml;
    }
  }
}
</script>