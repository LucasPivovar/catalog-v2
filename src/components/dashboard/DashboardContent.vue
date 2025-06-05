<template>
    <div class="dashboard">
      <!-- Dashboard content goes here -->
      <div class="dashboard-header">
        <h2>Dashboard Principal</h2>
      </div>
      <div class="dashboard-content">
        <div class="stats-cards">
          <div class="stat-card">
            <h3>Usuários Ativos</h3>
            <p class="stat-number">1,245</p>
            <p class="stat-change positive">+12% desde o último mês</p>
          </div>
          <div class="stat-card">
            <h3>Novos Registros</h3>
            <p class="stat-number">342</p>
            <p class="stat-change positive">+8% desde o último mês</p>
          </div>
          <div class="stat-card">
            <h3>Tempo Médio</h3>
            <p class="stat-number">24m</p>
            <p class="stat-change negative">-2% desde o último mês</p>
          </div>
        </div>
        
        <div class="recent-activity">
          <h3>Atividade Recente</h3>
          <div class="activity-list">
            <!-- Lista de atividades recentes aqui -->
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'DashboardContent'
  }
  </script>