<template>
    <section class="section section-shaped section-lg my-0">
        <div class="shape shape-style-1 bg-gradient-default">
            <span></span>
            <span></span>
            <span></span>
            <span></span>
            <span></span>
            <span></span>
            <span></span>
            <span></span>
        </div>
        <div class="container pt-lg-md">
            <div class="row justify-content-center">
                <div class="col-lg-5">
                    <card type="secondary" shadow
                          header-classes="bg-white pb-5"
                          body-classes="px-lg-5 py-lg-5"
                          class="border-0">
                        <template>
                            <!-- <div class="text-center text-muted mb-4">
                                <small>Sign in with your credentials</small>
                            </div> -->
                            <form role="form" @submit.prevent="login">
                                <div class="form-group">
                                    <label for="email">Email address</label>
                                    <input 
                                        type="email" 
                                        id="email" 
                                        v-model="email" 
                                        class="form-control" 
                                        placeholder="Enter your email"
                                        required
                                    />
                                </div>
                                <div class="form-group">
                                    <label for="password">Password</label>
                                    <input 
                                        type="password" 
                                        id="password" 
                                        v-model="password" 
                                        class="form-control" 
                                        placeholder="Enter your password"
                                        required
                                    />
                                </div>
                                <div class="text-center">
                                    <button type="submit" class="btn btn-primary my-4">Sign In</button>
                                </div>
                            </form>
                        </template>
                    </card>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      email: '',
      password: '',
    };
  },
  methods: {
    login() {
      const credentials = {
        email: this.email,
        password: this.password,
      };

      axios
        .post('http://localhost:3333/api/login', credentials, {withCredentials: true,})
        .then((response) => {
          if (response) {
            // Redirection vers la page de profil après une connexion réussie
            this.$router.push('/profile');
          } else {
            alert('Email ou mot de passe incorrect');
          }
        })
        .catch((error) => {
          console.error('Erreur lors de la connexion:', error);
          alert('Une erreur est survenue. Veuillez réessayer plus tard.');
        });
    },
  },
};
</script>

<style scoped>
/* Style de base pour le formulaire */
.form-group {
  margin-bottom: 1.5rem;
}

label {
  font-size: 1rem;
  font-weight: 600;
  color: #6c757d;
}

input[type="email"], input[type="password"] {
  padding: 0.75rem;
  font-size: 1rem;
  border: 1px solid #ced4da;
  border-radius: 0.375rem;
  width: 100%;
  box-sizing: border-box;
}

button {
  padding: 0.75rem 1.5rem;
  font-size: 1.25rem;
  border-radius: 0.375rem;
  border: none;
  background-color: #007bff;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

button:hover {
  background-color: #0056b3;
}

a {
  color: #17a2b8;
  text-decoration: none;
}

a:hover {
  text-decoration: underline;
}
</style>
