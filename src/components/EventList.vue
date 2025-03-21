<template>
    <div class="container my-5">
      <h1 class="text-center mb-5 fw-bold text-dark">🎉 Événements à venir</h1>
  
      <!-- Loader pendant le chargement -->
      <div v-if="loading" class="text-center">
        <div class="spinner-border text-primary" role="status">
          <span class="visually-hidden">Chargement...</span>
        </div>
      </div>
  
      <!-- Message d'erreur -->
      <div v-else-if="error" class="alert alert-danger text-center">
        <i class="bi bi-exclamation-triangle-fill me-2"></i>{{ error }}
      </div>
  
      <!-- Liste des événements -->
      <div v-else>
        <div v-if="events.length > 0" class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
          <div class="col" v-for="event in events" :key="event.id">
            <EventCard 
              :event="event" 
              @open-details-modal="openDetailsModal"
              @open-participation-modal="openParticipationModal"
            />
          </div>
        </div>
  
        <p v-else class="text-center text-muted">Aucun événement disponible pour le moment.</p>
      </div>
  
      <!-- Modal pour afficher les détails de l'événement -->
      <div 
        class="modal fade"
        :class="{ show: isDetailsModalOpen }"
        tabindex="-1"
        aria-labelledby="eventModalLabel"
        aria-hidden="true"
        style="display: block;"
        v-if="isDetailsModalOpen"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title fw-bold text-primary" id="eventModalLabel">
                {{ selectedEvent.title }}
              </h5>
              <button 
                type="button" 
                class="btn-close" 
                @click="closeDetailsModal"
                aria-label="Close"
              ></button>
            </div>
            <div class="modal-body">
              <!-- Description -->
              <p class="card-text text-muted mb-3">
                <i class="bi bi-card-text me-2"></i>
                {{ selectedEvent.description }}
              </p>
  
              <!-- Places disponibles -->
              <p class="card-text text-muted mb-2">
                <i class="bi bi-people-fill me-2"></i>Places disponibles : 
                <span class="fw-bold">{{ selectedEvent.place }}</span>
              </p>
  
              <!-- Dates -->
              <p class="card-text text-muted mb-2">
                <i class="bi bi-calendar-event me-2"></i>Début : 
                <span class="fw-bold">{{ selectedEvent.startDate }}</span>
              </p>
              <p class="card-text text-muted mb-3">
                <i class="bi bi-calendar-check me-2"></i>Fin : 
                <span class="fw-bold">{{ selectedEvent.endDate }}</span>
              </p>
  
              <!-- Statut -->
              <div class="mb-3">
                <span
                  class="badge px-3 py-2"
                  :class="{
                    'bg-success': selectedEvent.status === 'actif',
                    'bg-danger': selectedEvent.status === 'expiré',
                  }"
                >
                  {{ selectedEvent.status === 'actif' ? 'Disponible' : 'Expiré' }}
                </span>
              </div>
            </div>
            <div class="modal-footer">
              <button 
                type="button" 
                class="btn btn-secondary" 
                @click="closeDetailsModal"
              >
                Fermer
              </button>
            </div>
          </div>
        </div>
      </div>
  
      <!-- Modal pour participer à un événement -->
      <div 
        class="modal fade"
        :class="{ show: isParticipationModalOpen }"
        tabindex="-1"
        aria-labelledby="participationModalLabel"
        aria-hidden="true"
        style="display: block;"
        v-if="isParticipationModalOpen"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title fw-bold text-primary" id="participationModalLabel">
                Participer à {{ selectedEvent.title }}
              </h5>
              <button 
                type="button" 
                class="btn-close" 
                @click="closeParticipationModal"
                aria-label="Close"
              ></button>
            </div>
            <div class="modal-body">
              <!-- Formulaire de participation -->
              <form @submit.prevent="submitParticipation">
                <div class="mb-3">
                  <label for="firstname" class="form-label">Prénom</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="firstname" 
                    v-model="participationForm.firstname"
                    required
                  >
                </div>
                <div class="mb-3">
                  <label for="lastname" class="form-label">Nom</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="lastname" 
                    v-model="participationForm.lastname"
                    required
                  >
                </div>
                <div class="mb-3">
                  <label for="email" class="form-label">Email</label>
                  <input 
                    type="email" 
                    class="form-control" 
                    id="email" 
                    v-model="participationForm.email"
                    required
                  >
                </div>
                <button type="submit" class="btn btn-primary w-100">
                  <i class="bi bi-send me-2"></i>Envoyer
                </button>
              </form>
            </div>
          </div>
        </div>
      </div>
  
      <!-- Overlay pour les modals -->
      <div 
        class="modal-backdrop fade"
        :class="{ show: isDetailsModalOpen || isParticipationModalOpen }"
        v-if="isDetailsModalOpen || isParticipationModalOpen"
      ></div>
    </div>
  </template>
  
  <script>
  import axios from 'axios'
  import EventCard from './EventCard.vue'
  
  export default {
    components: {
      EventCard,
    },
    data() {
      return {
        events: [],
        loading: true,
        error: null,
        isDetailsModalOpen: false,
        isParticipationModalOpen: false,
        selectedEvent: null,
        participationForm: {
          firstname: '',
          lastname: '',
          email: '',
        },
      }
    },
    async created() {
      try {
        const response = await axios.get('http://localhost:3333/api/evenements')
  
        // Filtrer les événements non supprimés
        this.events = response.data.filter(event => !event.isDeleted)
      } catch (err) {
        this.error = "Impossible de récupérer les événements. Veuillez réessayer plus tard."
      } finally {
        this.loading = false
      }
    },
    methods: {
      openDetailsModal(event) {
        this.selectedEvent = event
        this.isDetailsModalOpen = true
      },
      closeDetailsModal() {
        this.isDetailsModalOpen = false
        this.selectedEvent = null
      },
      openParticipationModal(event) {
        this.selectedEvent = event
        this.isParticipationModalOpen = true
      },
      closeParticipationModal() {
        this.isParticipationModalOpen = false
        this.selectedEvent = null
        this.resetParticipationForm()
      },
      resetParticipationForm() {
        this.participationForm = {
            firstname: '',
            lastname: '',
            email: '',
        }
      },
      async submitParticipation() {
        try {
          const payload = {
            ...this.participationForm,
            eventId: this.selectedEvent.id, // Ajouter l'ID de l'événement
          }
  
          // Envoyer les données à l'API
          await axios.post('http://localhost:3333/api/participations', payload)
  
          // Fermer le modal et réinitialiser le formulaire
          this.closeParticipationModal()
          alert('Votre participation a été enregistrée avec succès !')
        } catch (err) {
          alert('Une erreur est survenue. Veuillez réessayer plus tard.')
        }
      },
    },
  }
  </script>
  
  <style scoped>
  h1 {
    font-size: 2rem;
    letter-spacing: 1px;
  }
  
  .modal-backdrop {
    background-color: rgba(0, 0, 0, 0.5);
  }
  
  .modal {
    display: none;
  }
  
  .modal.show {
    display: block;
  }
  </style>