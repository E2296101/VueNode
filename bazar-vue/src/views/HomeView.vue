<template>
<div class="jumbotron mt-3 position-relative">
      <img :src="require(`@/assets/img/bb.jpg`)" alt="BAZAR" class="img-fluid" />
      <div class="overlay">
        <h1 class="display-4">Bienvenue au bazar de l'electromenager !</h1>
        <p class="lead">
          Découvrez notre large sélection de produits de qualité.
        </p>
        
         <RouterLink to="/product" class="btn btn-primary btn-lg">Voir les produits</RouterLink>
      </div>
    </div>
</template>


