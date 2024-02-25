
<template>
    <div id="app" class="form-container">
      <form @submit.prevent="submitForm">
        <div v-if="step === 1">
          <p>Possédez-vous déjà un 3e pillier ?</p>
          <label>
            <input type="radio" value="oui" v-model="form.thirdPillar" /> Oui
          </label>
          <label>
            <input type="radio" value="non" v-model="form.thirdPillar" /> Non
          </label>
          <button @click="nextStep">Continuer</button>
        </div>
  
        <div v-if="step === 2">
          <p>Êtes-vous un homme ou une femme?</p>
          <label>
            <input type="radio" value="homme" v-model="form.gender" /> Homme
          </label>
          <label>
            <input type="radio" value="femme" v-model="form.gender" /> Femme
          </label>
          <button @click="prevStep">Précédent</button>
          <button @click="nextStep">Continuer</button>
        </div>
  
        <div v-if="step === 3">
          <p>Votre épargne mensuelle idéale ?</p>
          <input type="number" v-model.number="form.monthlySavings" min="0" />
          <button @click="prevStep">Précédent</button>
          <button @click="nextStep" :disabled="form.monthlySavings <= 0">Continuer</button>
        </div>
  
        <div v-if="step === 4">
            <p>Votre année de naissance ?</p>
            <input type="number" v-model.number="form.birthYear" :max="currentYear" :min="currentYear - 100" />
            <button @click="prevStep">Précédent</button>
            <button @click="nextStep" :disabled="!isValidBirthYear(form.birthYear)">Continuer</button>
        </div>
  
        <div v-if="step === 5">
          <p>Quel est votre lieu de résidence ? (npa localité)</p>
          <input type="text" v-model="form.residence" />
          <button @click="prevStep">Précédent</button>
          <button @click="nextStep">Calculer</button>
        </div>

        <LoadingAnimation v-if="step === 6" @animation-done="handleAnimationDone" />

        <div v-if="step === 7">
            <h2>Vous pourrez obtenir un capital de {{ calculatedCapital }} CHF</h2>
            <input type="email" v-model="form.email" placeholder="Votre e-mail" required />
            <input type="tel" v-model="form.phoneNumber" placeholder="Votre numéro de téléphone" required />
            <button type="submit" @click="submitForm">Découvrir mes offres</button>
        </div>

      </form>
    </div>
  </template>
  
  <script>
  import LoadingAnimation from './LoadingAnimation.vue';
  export default {
    components: {
        LoadingAnimation
    },
    data() {
      return {
        currentYear: new Date().getFullYear(),
        step: 1,
        form: {
          thirdPillar: null,
          gender: null,
          monthlySavings: 0,
          birthYear: null,
          residence: '',
          email : '',
          phoneNumber: ''
        }
      };
    },

    computed: {
        isValidBirthYear() {
            return (year) => year && year <= this.currentYear && year >= this.currentYear - 100;
        },

        calculatedCapital() {
            const currentYear = new Date().getFullYear();
            const age = currentYear - this.form.birthYear;
            const retirementAge = this.form.gender === 'femme' ? 64 : 65;
            const yearsToRetirement = retirementAge - age;
            const interestRate = 0.05;
            let capital = 0;
            for (let i = 0; i < yearsToRetirement; i++) {
                capital = (capital + this.form.monthlySavings * 12) * (1 + interestRate);
            }
            return capital.toFixed(2);
        }
    },

    methods: {
      nextStep() {
        if (this.step < 10) {
          this.step++;
        }
      },
      prevStep() {
        if (this.step > 1) {
          this.step--;
        }
      },
      submitForm() {
        let formData = {
        thirdPillar: this.form.thirdPillar,
        gender: this.form.gender,
        monthlySavings: this.form.monthlySavings,
        birthYear: this.form.birthYear,
        residence: this.form.residence,
        email: this.form.email,
        phoneNumber: this.form.phoneNumber,
        calculatedCapital: this.calculatedCapital
        };

        fetch('https://emedias.ch/sendEmail.php', {
        method: 'POST',
        headers: {
            'Content-Type': 'application/json'
        },
        body: JSON.stringify(formData)
        })
        .then(response => response.json())
        .then(data => {
        console.log(data);
        })
        .catch(error => {
        console.error('Error:', error);
        });
      },

      handleAnimationDone() {
        this.step = 7;
    },
    }
  };
  </script>



<style>
.form-container {
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  background-color: #f9f9f9;
}

form {
  display: flex;
  flex-direction: column;
}

p {
  font-size: 18px;
  color: #333;
}

input[type="radio"] {
  margin-right: 10px;
}

input[type="number"],
input[type="text"] {
  padding: 10px;
  margin: 10px 0;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 10px;
  margin: 10px 0;
  border: none;
  border-radius: 4px;
  background-color: #5c6bc0;
  color: white;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #3f51b5;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
  