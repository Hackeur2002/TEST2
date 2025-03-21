<template>
    <div class="card h-100 shadow-sm border-0">
      <div class="card-body">
        <!-- Titre -->
        <h5 class="card-title fw-bold text-primary">{{ event.title }}</h5>
  
        <!-- Places disponibles -->
        <p class="card-text text-muted mb-2">
          <i class="bi bi-people-fill me-2"></i>Places disponibles : 
          <span class="fw-bold">{{ event.place }}</span>
        </p>
  
        <!-- Dates -->
        <p class="card-text text-muted mb-2">
          <i class="bi bi-calendar-event me-2"></i>Début : 
          <span class="fw-bold">{{ event.startDate }}</span>
        </p>
        <p class="card-text text-muted mb-3">
          <i class="bi bi-calendar-check me-2"></i>Fin : 
          <span class="fw-bold">{{ event.endDate }}</span>
        </p>
  
        <!-- Statut -->
        <div class="mb-3">
          <span
            class="badge px-3 py-2"
            :class="{
              'bg-success': event.status === 'actif',
              'bg-danger': event.status === 'expiré',
            }"
          >
            {{ event.status === 'actif' ? 'Disponible' : 'Expiré' }}
          </span>
        </div>
  
        <!-- Bouton Participer -->
        <button 
          class="btn btn-primary w-100 mb-2"
          :disabled="event.status !== 'actif'"
          @click="openParticipationModal"
        >
          <i class="bi bi-check-circle me-2"></i>Participer
        </button>
  
        <!-- Bouton Voir plus -->
        <button 
          class="btn btn-outline-secondary w-100"
          @click="openDetailsModal"
        >
          <i class="bi bi-eye me-2"></i>Voir plus
        </button>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      event: {
        type: Object,
        required: true,
      },
    },
    methods: {
      openDetailsModal() {
        // Émettre un événement pour ouvrir le modal des détails
        this.$emit('open-details-modal', this.event)
      },
      openParticipationModal() {
        // Émettre un événement pour ouvrir le modal de participation
        this.$emit('open-participation-modal', this.event)
      },
    },
  }
  </script>
  
  <style scoped>
  .card {
    transition: transform 0.2s ease-in-out, box-shadow 0.2s ease-in-out;
    border-radius: 10px;
  }
  
  .card:hover {
    transform: translateY(-5px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  }
  
  .btn-primary {
    border-radius: 8px;
  }
  </style>