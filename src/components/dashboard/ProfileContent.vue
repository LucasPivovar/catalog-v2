<template>
  <main>
    <div class="profile">
      <div class="profile-header">
        <h2>Configurações de Perfil</h2>
      </div>
      <div class="profile-content">
        <div class="profile-form">
          <h3>Informações Pessoais</h3>
          <p>Atualize suas informações de perfil</p>
          <div class="form-group">
              <label for="name">Nome Exibido</label>
              <div class="input-container">
                  <input type="text" id="name" class="input-field" placeholder="Seu nome" />
              </div>
          </div>
          
          <div class="form-group">
              <label for="bio">Biografia</label>
              <div class="input-container">
                  <textarea id="bio" class="input-field" placeholder="Conte nos um pouco sobre você"></textarea>
              </div>
          </div>
  
              <div class="form-group ">
                  <label for="whatsapp">WhatsApp</label>
                  <div class="input-container">
                      <input type="text" placeholder="+55 (00) 00000-0000" id="whatsapp" class="input-field">
                  </div>
              </div>
  
              <div class="form-group ">
                  <label for="telegram">Telegram</label>
                  <div class="input-container">
                      <input type="text" placeholder="@usuário" id="telegram" class="input-field">
                  </div>    
              </div>
  
              <div class="form-group ">
                  <label for="instagram">Instagram</label>
                  <div class="input-container">
                      <input type="text" placeholder="Link do perfil" id="instagram" class="input-field">
                  </div>
              </div>
              <div class="form-group">
                  <label for="Perfil">Link Externo</label>
                    <div class="input-container">
                      <input type="text" placeholder="Link Externo" id="Perfil" class="input-field">
                  </div>
              </div>
          <button class="submit-button red">Salvar Alterações</button>
        </div>  
  
        <div class="profile-form">
          <div class="form-group">
              <h3>Fotos</h3>
              <p>Adicione até 10 fotos ao seu perfil</p>
              <div class="input-container">
                  <input 
                    type="file" 
                    id="photos" 
                    ref="photoInput"
                    class="photo-input" 
                    @change="handlePhotoUpload" 
                    accept="image/*"
                    multiple
                    :disabled="photos.length >= 10"
                  >
              </div>
              
              <div class="photo-preview-container">
                <div v-for="(photo, index) in photos" :key="index" class="photo-preview" @click="removePhoto(index)">
                  <img :src="photo.url" alt="Preview" class="preview-image">
                </div>
                
                <label for="photos" class="photo-preview add-photo" v-if="photos.length < 10">
                  <div class="add-icon">+</div>
                  <div class="add-text">Adicionar</div>
                </label>
              </div>
              <p>{{ photos.length }}/10 fotos adicionadas</p>
          </div>
        </div>
  
        <div class="profile-form">
          <div class="form-group">
              <label for="password">Nova Senha</label>
              <div class="input-container">
                <input type="text" class="input-field" placeholder="Digite sua nova senha" id="password">
              </div>
          </div>
  
          <div class="form-group">
              <label for="confirm-password">Confirmar Senha</label>
              <div class="input-container">
                <input type="text" class="input-field" placeholder="Digite sua nova senha" id="confirm-password">
              </div>
          </div>
          <button class="submit-button red">Alterar Senha</button>
        </div>
      </div>
    </div>
  </main>
</template>

<script>
import '@/assets/css/global.css';
import '@/assets/css/profile_content.css';

export default {
  name: 'ProfileView',
  data() {
    return {
      showPassword: false,
      photos: [],
      maxPhotos: 10
    }
  },
  methods: {
    handlePhotoUpload(event) {
      const files = event.target.files;
      
      if (!files.length) return;
      
      const remainingSlots = this.maxPhotos - this.photos.length;
      if (remainingSlots <= 0) {
        alert('Você já atingiu o limite máximo de 10 fotos.');
        return;
      }
  
      const filesToProcess = Array.from(files).slice(0, remainingSlots);
      
      filesToProcess.forEach(file => {
        if (!file.type.match('image.*')) {
          alert('Por favor, selecione apenas arquivos de imagem.');
          return;
        }
        
        const reader = new FileReader();
        reader.onload = (e) => {
          this.photos.push({
            file: file,
            url: e.target.result,
            name: file.name
          });
        };
        reader.readAsDataURL(file);
      });
      
      this.$refs.photoInput.value = '';
    },
    
    removePhoto(index) {
      this.photos.splice(index, 1);
    },
    
    uploadPhotos() {
      const formData = new FormData();
      
      this.photos.forEach((photo, index) => {
        formData.append(`photo${index}`, photo.file);
      });
      
      console.log('Fotos prontas para upload:', this.photos);
    }
  }
}
</script>