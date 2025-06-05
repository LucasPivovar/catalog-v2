<template>
  <header class="app-header">
    <h1><strong>Studio</strong> Catalog</h1>
    <div class="header-left">
      <p class="models-count">
        <img src="../assets/icons/people.svg" alt="Models icon">
        {{ modelsCount }} modelos
      </p>
      <a href="/access" class="button">Entrar</a>
    </div>
  </header>
</template>

<script>
export default {
  name: 'AppHeader',
  props: {
    modelsCount: {
      type: Number,
      default: 0
    }
  }
}
</script>

<style scoped>
.app-header {
  display: flex;
  color: var(--text-primary);
  align-items: center;
  justify-content: space-between;
  padding: 0 35px;
  min-width: 1400px; 
  font-family: "Inter", sans-serif;
}

.header-left {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 20px;
}

.app-header h1 {
  font-weight: 400;
  font-size: 1.6rem;
  margin: 0;
}

.app-header strong {
  font-weight: 400;
  color: var(--text-accent);
}

.models-count {
  font-size: 1.1rem;
  font-weight: 300;
  display: flex;
  justify-content: center;
  flex-direction: row;
  align-items: center;
  gap: 5px;
  margin: 0;
}

.models-count img {
  width: 25px;
  height: 25px;
}

.button {
  color: var(--text-primary);
  text-decoration: none;
  transition: all 0.3s ease;
  font-weight: 500;
  padding: 8px 15px;
  background: var(--primary);
  border-radius: 15px;
  display: inline-flex;
  align-items: center;
}

.button:hover {
  opacity: 0.8;
  transform: translateY(-1px);
}

@media (max-width: 1400px) {
  .app-header {
    width: 100%;
    min-width: auto;
    max-width: 100%;
    padding: 0 20px;
  }
}

@media (max-width: 768px) {
  .app-header {
    flex-direction: row;
    gap: 15px;
    padding: 0 20px;
    text-align: center;
  }
  
  .header-left {
    flex-direction: column;
    gap: 10px;
  }
  
  .app-header h1 {
    font-size: 1.4rem;
  }
  
  .models-count {
    font-size: 1rem;
  }

  .header-left{
    flex-direction: row;
  }
}

@media (max-width: 480px) {
  .app-header {
    padding: 0 15px;
  }
  
  .app-header h1 {
    font-size: 1.2rem;
  }
  
  .models-count {
    font-size: 0.9rem;
  }
  
  .button {
    padding: 6px 12px;
    font-size: 0.9rem;
  }
}
</style>