<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Inscription</h1>
    <form @submit.prevent="handleRegister" class="form-register d-flex flex-column justify-content-center">
      <!-- Informations personnelles -->
      <div class="form-section">
        <h5>Informations personnelles</h5>
        <div class="form-group">
          <label for="nomuser">Nom :</label>
          <input type="text" v-model="client.nomUser" id="nomuser" class="form-control" required maxlength="50"
            placeholder="Saisissez votre nom">
        </div>
        <div class="form-group">
          <label for="prenomuser">Prénom :</label>
          <input type="text" v-model="client.prenomUser" id="prenomuser" class="form-control" required maxlength="50"
            placeholder="Saisissez votre prénom">
        </div>
        <div class="form-group">
          <label for="genreuser">Genre :</label>
          <select v-model="client.genreUser" id="genreuser" class="form-control" required>
            <option value="" disabled>Choisissez votre genre</option>
            <option value="Monsieur">Monsieur</option>
            <option value="Madame">Madame</option>
          </select>
        </div>
        <div class="form-group">
          <label for="datenaissance">Date de naissance :</label>
          <input type="date" v-model="client.dateNaissance" id="datenaissance" class="form-control" required>
          <small>Vous devez avoir au moins 18 ans.</small>
        </div>
      </div>
      <!-- Coordonnées -->
      <div class="form-section">
        <h5>Coordonnées</h5>
        <div class="form-group">
          <label for="telephone">Téléphone :</label>
          <input type="tel" v-model="client.telephone" id="telephone" class="form-control" required
            placeholder="06XXXXXXXX">
        </div>
        <div class="form-group">
          <label for="emailuser">Email :</label>
          <input type="email" v-model="client.emailUser" id="emailuser" class="form-control" required
            placeholder="exemple@mail.com">
        </div>
        <div class="form-group">
          <label for="libelleadresse">Adresse :</label>
          <input type="text" v-model="client.adresse" id="libelleadresse" class="form-control" required maxlength="100"
            placeholder="Saisissez votre adresse">
        </div>
        <div class="form-group">
          <label for="nomville">Ville :</label>
          <input type="text" v-model="client.ville" id="nomville" class="form-control" required maxlength="50"
            placeholder="Saisissez la ville">
        </div>
        <div class="form-group">
          <label for="codepostal">Code Postal :</label>
          <input type="text" v-model="client.codePostal" id="codepostal" class="form-control" required pattern="^\d{5}$"
            placeholder="Saisissez votre code postal" maxlength="5">
        </div>
      </div>
      <!-- Sécurité -->
      <div class="form-section">
        <h5>Sécurité</h5>
        <div class="form-group">
          <label for="motdepasseuser">Mot de passe :</label>
          <input type="password" v-model="client.motdepasseuser" id="motdepasseuser" class="form-control" required
            minlength="8" placeholder="Saisissez un mot de passe sécurisé" @input="checkPasswordStrength">
        </div>
        <div class="form-group">
          <label for="motdepasseuser_confirmation">Confirmation du mot de passe :</label>
          <input type="password" v-model="passwordConfirmation" id="motdepasseuser_confirmation" class="form-control"
            required placeholder="Confirmez votre mot de passe">
        </div>
      </div>
      <button type="submit" class="btn-login" :disabled="isLoading">S'inscrire</button>
    </form>
  </div>
</template>

<script>
import { ref } from 'vue';
import { useUserStore } from "@/stores/userStore";

export default {
  setup() {
    const store = useUserStore();
    const client = ref({
      nomUser: '',
      prenomUser: '',
      genreUser: '',
      dateNaissance: '',
      telephone: '',
      emailUser: '',
      adresse: '',
      ville: '',
      codePostal: '',
      motdepasseuser: '',
      typeClient: 'Uber'  // Ajout du typeClient par défaut
    });
    const passwordConfirmation = ref('');
    const isLoading = ref(false);
    const errorMessage = ref('');

    const handleRegister = async () => {
      if (client.value.motdepasseuser !== passwordConfirmation.value) {
        errorMessage.value = "Les mots de passe ne correspondent pas.";
        return;
      }

      isLoading.value = true;
      try {
        await store.register(client.value);
      } catch (error) {
        if (error.response && error.response.data.errors) {
          const errors = error.response.data.errors;
          errorMessage.value = '';

          if (errors.EmailUser) {
            errorMessage.value += "Erreur sur l'email : " + errors.EmailUser.join(", ") + ". ";
          }
          if (errors.TypeClient) {
            errorMessage.value += "Erreur sur le type de client : " + errors.TypeClient.join(", ") + ". ";
          }
        } else {
          errorMessage.value = "Erreur lors de l'inscription.";
        }
        console.error(error);
      } finally {
        isLoading.value = false;
      }
    };

    return {
      client,
      passwordConfirmation,
      isLoading,
      errorMessage,
      handleRegister
    };
  }
};
</script>

<style scoped>
#app h1 {
  font-size: 52px;
  font-weight: 700;
  line-height: 64px;
}

#app h3 {
  font-size: 36px;
  font-weight: 700;
  line-height: 44px;
}

body {
  background-color: #f9f9f9;
  color: #333;
  margin: 0;
  padding: 0;
}

a {
  color: #818181;
}

a:hover {
  color: #747474;
  text-decoration: black solid underline;
}

h1,
h5,
h2 {
  font-weight: bold;
  color: #000;
  margin: 20px 0;
}

/* Alert Messages */
.alert-message {
  font-size: 14px;
  font-weight: 500;
  padding: 10px 15px;
  border-radius: 5px;
  margin-bottom: 15px;
}

.alert-message.success {
  background-color: #e8f5e9;
  color: #4CAF50;
  border: 1px solid #4CAF50;
}

.alert-message.error {
  background-color: #ffebee;
  color: #F44336;
  border: 1px solid #F44336;
}

/* Card Style */
.card {
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  padding: 20px;
  margin-bottom: 20px;
}

.card h2 {
  font-size: 1.5rem;
  color: #000;
  margin-bottom: 15px;
}

/* Form Styles */
.form-register {
  max-width: 400px;
  margin: auto;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  padding: 25px 20px;
}

.form-section {
  margin-bottom: 20px;
  padding: 20px;
  border-radius: 10px;
  background-color: #f5f5f5;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}

.form-section h5 {
  font-size: 1.1rem;
  margin-bottom: 15px;
  color: #000;
}

/* Input Fields */
label {
  font-weight: 600;
  margin-bottom: 5px;
  display: block;
}

input,
select,
textarea {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  font-size: 1rem;
  box-sizing: border-box;
}

input:focus,
select:focus,
textarea:focus {
  outline: none;
  border-color: #000;
  box-shadow: 0 0 3px rgba(0, 0, 0, 0.2);
}

/* Buttons */
.btn-login {
  cursor: pointer;
  width: 100%;
  font-size: 14px;
  font-weight: 600;
  background: #000;
  color: #fff;
  border-radius: 8px;
  padding: 0.7rem 1rem;
  border: none;
  transition: background 0.3s ease;
}

.btn-login:hover {
  background: #333;
  color: #fff;
}

/* Password Strength Indicator */
#password-strength {
  font-size: 0.85rem;
  margin-top: 5px;
  font-weight: 500;
}

.password-requirements {
  list-style: none;
}

/* Checkbox and Radio */
input[type="radio"],
input[type="checkbox"] {
  width: auto;
  margin-right: 10px;
}

.form-checkbox {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
  gap: 10px;
}

.checkbox-input {
  accent-color: #000;
  width: 1.2rem;
  height: 1.2rem;
  margin: 0;
  cursor: pointer;
}

/* Responsive Design */
@media (max-width: 768px) {
  body {
    padding: 15px;
  }

  .form-register {
    padding: 20px;
    max-width: 100%;
  }

  input,
  select,
  textarea {
    font-size: 0.9rem;
    padding: 8px;
  }
}
</style>
