<template>
  <html lang="fr">
    <head>
      <meta charSet="UTF-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1.0" />
      <title>Le Bazar</title>
    </head>
    <body >
      <nav className="navbar navbar-expand-lg navbar-dark bg-dark  px-3">
        <RouterLink to="/" class="navbar-brand">Le Bazar</RouterLink>
        <button
          className="navbar-toggler"
          type="button"
          data-toggle="collapse"
          data-target="#navbarSupportedContent"
          aria-controls="navbarSupportedContent"
          aria-expanded="false"
          aria-label="Toggle navigation"
        >
          <span className="navbar-toggler-icon"></span>
        </button>

        <div className="collapse navbar-collapse" id="navbarSupportedContent">
          <ul className="navbar-nav ml-auto">
            <li >
              <RouterLink to="/" class="nav-link">Accueil</RouterLink>
            </li>
            <li >
              <RouterLink to="/product" class="nav-link">Produits</RouterLink>
            </li>
          </ul>
        </div>
      </nav>
      <router-view
      :produits="produits"
      :ajouterProduit="ajouterProduit"
      :supprimerProduit="supprimerProduit"
      :modifierProduit = "modifierProduit"
      />
      <footer className="footer mt-auto bg-dark text-white text-center py-3">
          <p>&copy; 2023 Le Bazar. Tous droits réservés.</p>
      </footer>
    </body>
  </html>
</template>
<script>
import ProductDataService from '@/services/ProductDataService'
export default {
  data () {
    return {
      produits: [],
    }
  },
  methods: {

     ajouterProduit (data) {
      this.produits.push(data)
     // console.log(this.produits)
    },
    supprimerProduit(index){
      this.produits.splice(index,1) 
    },
    modifierProduit(index, data){
      this.produits[index].nom = data.nom
      this.produits[index].description = data.description
      this.produits[index].prix = data.prix
      this.produits[index].categorie = data.categorie
    }
  },
 
  mounted () {
    ProductDataService.getAll()
      .then(response => {
        this.produits = response.data 
      })
     
      
  }
}
</script>


