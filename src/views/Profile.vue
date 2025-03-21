<template>
    <div class="profile-page">
      <section class="section section-skew">
        <div class="container">
          <div class="row">
            <div class="col-lg-3">
              <div class="list-group">
                <button
                  class="list-group-item list-group-item-action"
                  :class="{ active: tab === 'evenements' }"
                  @click="tab = 'evenements'"
                >
                  Événements
                </button>
                <button
                  class="list-group-item list-group-item-action"
                  :class="{ active: tab === 'participants' }"
                  @click="tab = 'participants'"
                >
                  Participants
                </button>
              </div>
            </div>
            <div class="col-lg-9">
              <div v-if="tab === 'evenements'">
                <h3>Liste des événements</h3>
                <button class="btn btn-success mb-3" @click="openModal('create')">Créer un événement</button>
                <ul class="list-group">
                  <li class="list-group-item" v-for="evenement in evenements" :key="evenement.id">
                    <div>
                      <h5>{{ evenement.title }}</h5>
                      <p>{{ evenement.description }}</p>
                      <small>Du {{ evenement.startDate }} au {{ evenement.endDate }}</small>
                      <div class="mt-2">
                        <button class="btn btn-danger btn-sm" @click="deleteEvenement(evenement.id)">Supprimer</button>
                        <button class="btn btn-primary btn-sm ml-2" @click="openModal('edit', evenement)">Modifier</button>
                      </div>
                    </div>
                  </li>
                </ul>
              </div>
              <div v-if="tab === 'participants'">
                <h3>Liste des participants</h3>
                <ul class="list-group">
                  <li class="list-group-item" v-for="participation in participations" :key="participation.id">
                    <div>
                      <h5>{{ participation.users.firstname }} {{ participation.users.lastname }}</h5>
                      <p>Email: {{ participation.users.email }}</p>
                      <p>Événement: {{ participation.evenements.title }}</p>
                      <p>Code de l'événement: {{ participation.evenements.code }}</p>
                    </div>
                  </li>
                </ul>
              </div>
            </div>
          </div>
        </div>
      </section>
  
      <!-- Modal pour créer ou modifier un événement -->
      <div v-if="modalOpen" class="modal fade show" style="display: block;" tabindex="-1" role="dialog">
        <div class="modal-dialog" role="document">
          <div class="modal-content">
            <div class="modal-header">
              <h5 class="modal-title">{{ modalType === 'create' ? 'Créer un événement' : 'Modifier un événement' }}</h5>
              <button type="button" class="close" @click="closeModal" aria-label="Close">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="submitEventForm">
                <div class="form-group">
                  <label for="title">Titre</label>
                  <input type="text" class="form-control" id="title" v-model="currentEvenement.title" required />
                </div>
                <div class="form-group">
                  <label for="description">Description</label>
                  <textarea class="form-control" id="description" v-model="currentEvenement.description" required></textarea>
                </div>
                <div class="form-group">
                  <label for="startDate">Date de début</label>
                  <input type="date" class="form-control" id="startDate" v-model="currentEvenement.startDate" required />
                </div>
                <div class="form-group">
                  <label for="endDate">Date de fin</label>
                  <input type="date" class="form-control" id="endDate" v-model="currentEvenement.endDate" required />
                </div>
                <button type="submit" class="btn btn-primary">Enregistrer</button>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
<script>
import axios from "axios";

export default {
  data() {
    return {
      tab: "evenements",
      evenements: [],
      participations: [],
      isAuthenticated: false,
      modalOpen: false, // état pour ouvrir/fermer le modal
      modalType: "", // type de modal ('create' ou 'edit')
      currentEvenement: {
        title: "",
        description: "",
        startDate: "",
        endDate: ""
      }
    };
  },
  mounted() {
    this.checkAuthentication();
  },
  methods: {
    checkAuthentication() {
      axios
        .get("http://localhost:3333/api/auth/check", { withCredentials: true })
        .then((response) => {
          if (!response.data.isAuthenticated) {
            this.$router.push("/login");
          } else {
            this.isAuthenticated = true;
            this.fetchEvenements();
            this.fetchParticipations();
          }
        })
        .catch((error) => {
          console.error("Erreur de vérification d'authentification:", error);
          this.$router.push("/login");
        });
    },
    fetchEvenements() {
      axios
        .get("http://localhost:3333/api/evenements", { withCredentials: true })
        .then((response) => {
          this.evenements = response.data;
        })
        .catch((error) => {
          console.error("Erreur de récupération des événements:", error);
        });
    },
    fetchParticipations() {
      axios
        .get("http://localhost:3333/api/participations", { withCredentials: true })
        .then((response) => {
          this.participations = response.data;
        })
        .catch((error) => {
          console.error("Erreur de récupération des participations:", error);
        });
    },
    openModal(type, evenement = null) {
      this.modalType = type;
      if (type === 'edit' && evenement) {
        this.currentEvenement = { ...evenement }; // Pré-charger les données pour modification
      } else {
        this.currentEvenement = { title: "", description: "", startDate: "", endDate: "" }; // Réinitialiser pour création
      }
      this.modalOpen = true;
    },
    closeModal() {
      this.modalOpen = false;
    },
    submitEventForm() {
      if (this.modalType === "create") {
        this.createEvenement();
      } else {
        this.updateEvenement();
      }
    },
    createEvenement() {
      axios
        .post("http://localhost:3333/api/evenements", this.currentEvenement, { withCredentials: true })
        .then((response) => {
          this.evenements.push(response.data);
          this.closeModal();
          alert("Événement créé !");
        })
        .catch((error) => {
          console.error("Erreur de création de l'événement:", error);
        });
    },
    updateEvenement() {
      axios
        .put(`http://localhost:3333/api/evenements/${this.currentEvenement.id}`, this.currentEvenement, { withCredentials: true })
        .then((response) => {
          const index = this.evenements.findIndex(evenement => evenement.id === this.currentEvenement.id);
          if (index !== -1) {
            this.evenements[index] = response.data;
          }
          this.closeModal();
          alert("Événement mis à jour !");
        })
        .catch((error) => {
          console.error("Erreur de mise à jour de l'événement:", error);
        });
    },
    deleteEvenement(id) {
      axios
        .delete(`http://localhost:3333/api/evenements/${id}`, { withCredentials: true })
        .then(() => {
          this.evenements = this.evenements.filter((evenement) => evenement.id !== id);
          alert("Événement supprimé !");
        })
        .catch((error) => {
          console.error("Erreur de suppression de l'événement:", error);
        });
    }
  }
};
</script>
