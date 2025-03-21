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
                <ul class="list-group">
                  <li class="list-group-item" v-for="evenement in evenements" :key="evenement.id">
                    <div>
                      <h5>{{ evenement.title }}</h5>
                      <p>{{ evenement.description }}</p>
                      <small>Du {{ evenement.startDate }} au {{ evenement.endDate }}</small>
                      <div class="mt-2">
                        <button class="btn btn-danger btn-sm" @click="deleteEvenement(evenement.id)">Supprimer</button>
                        <button class="btn btn-primary btn-sm ml-2" @click="editEvenement(evenement.id)">Modifier</button>
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
    </div>
  </template>
  
  <script>
  import axios from "axios";
  import { useRouter } from 'vue-router'; // Si tu utilises Vue Router pour la redirection
  
  export default {
    data() {
      return {
        tab: "evenements", 
        evenements: [],
        participations: [],
        isAuthenticated: false,  // Ajout de l'état d'authentification
      };
    },
    mounted() {
      // Vérification de l'authentification de l'utilisateur dès que le composant est monté
      this.checkAuthentication();
    },
    methods: {
      // Vérification de l'authentification
      checkAuthentication() {
        axios
          .get("http://localhost:3333/api/auth/check", {withCredentials: true})
          .then((response) => {
            if (!response.data.isAuthenticated) {
                // console.log(response)
              this.$router.push("/login"); // Redirection vers la page de login si non authentifié
            } else {
              this.isAuthenticated = true; // Utilisateur authentifié
              this.fetchEvenements();
              this.fetchParticipations();
            }
          })
          .catch((error) => {
            console.error("Erreur de vérification d'authentification:", error);
            this.$router.push("/login"); // Redirige vers la page de login en cas d'erreur
          });
      },
      fetchEvenements() {
        axios
          .get("http://localhost:3333/api/evenements")
          .then((response) => {
            this.evenements = response.data;
          })
          .catch((error) => {
            console.error("Erreur de récupération des événements:", error);
          });
      },
      fetchParticipations() {
        axios
          .get("http://localhost:3333/api/participations")
          .then((response) => {
            this.participations = response.data;
          })
          .catch((error) => {
            console.error("Erreur de récupération des participations:", error);
          });
      },
      deleteEvenement(id) {
        axios
          .delete(`http://localhost:3333/api/evenements/${id}`)
          .then(() => {
            this.evenements = this.evenements.filter((evenement) => evenement.id !== id);
            alert("Événement supprimé !");
          })
          .catch((error) => {
            console.error("Erreur de suppression de l'événement:", error);
          });
      },
      editEvenement(id) {
        alert(`Modification de l'événement avec ID: ${id}`);
      },
      createEvenement() {
        const newEvenement = {
          title: "Nouveau Événement",
          description: "Description de l'événement",
          startDate: "2025-05-01",
          endDate: "2025-05-02",
        };
  
        axios
          .post("http://localhost:3333/api/evenements", newEvenement)
          .then((response) => {
            this.evenements.push(response.data);
            alert("Événement créé !");
          })
          .catch((error) => {
            console.error("Erreur de création de l'événement:", error);
          });
      },
    },
  };
  </script>
  
  <style scoped>
  .list-group-item.active {
    background-color: #007bff;
    color: white;
  }
  </style>
  