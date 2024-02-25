<template>
    <div class="loading-container">
      <h2>Chargement de vos résultats</h2>
      <div v-for="(step, index) in steps" :key="index" class="loading-step">
        <span>{{ step.label }}</span>
        <div class="progress-bar-container">
          <div :class="{ 'progress-bar': true, 'filled': index < currentStep }"></div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        steps: [
          { label: "Analyse de votre situation", complete: false },
          { label: "Calcul de votre économie d'impôts", complete: false },
          { label: "Calcul de votre 3e pilier", complete: false },
          { label: "Préparation de votre proposition", complete: false }
        ],
        currentStep: 0,
      };
    },
    mounted() {
      this.fillProgress();
    },
    methods: {
      fillProgress() {
        const interval = setInterval(() => {
          if (this.currentStep < this.steps.length) {
            this.currentStep++;
          } else {
            clearInterval(interval);
            this.$emit('animation-done'); // Émettre un événement quand l'animation est terminée
          }
        }, 1000);
      }
    }
  }; 
  </script>
  
  <style>
  .loading-container {
    text-align: center;
  }
  
  .loading-step {
    margin: 10px 0;
  }
  
  .progress-bar-container {
    position: relative;
    height: 20px;
    background-color: #eee;
    border-radius: 10px;
  }
  
  .progress-bar {
    height: 100%;
    background-color: #5c6bc0;
    width: 0%;
    border-radius: 10px;
    transition: width 1s ease-in-out;
  }
  
  .filled {
    width: 100%; /* Full width when the step is marked as complete */
  }
  </style>
  