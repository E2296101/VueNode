<template>
  <div class="container mt-3">
    <h1>Liste des produits</h1>

    <button class="btn btn-primary mb-3" data-toggle="modal" data-target="#ajouterProduitModal" @click="openAjouterProduitModal" >
      Ajouter un nouvel article
    </button>
    <div v-show="message" class="alert alert-success">
        <span>{{ message }}</span>
    </div>
    <table class="table">
       
      <thead class="thead-dark">
        <tr>
        <th scope="col"></th>
          <th scope="col">Nom</th>
          <th scope="col">Description</th>
          <th scope="col">Prix</th>
          <th scope="col">Catégorie</th>
          <th scope="col">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="produits.length === 0">
          <td colspan="5" class="text-center mt-3">
            <p class="text-center mt-3 fw-bold fs-5">
              Aucun produit disponible
            </p>
          </td>
        </tr>
        <tr v-else v-for="produit in produits" :key="produit.id">
          <td>
            <img class="card-img-top mb-5 mb-md-0 image-produit" :src="require(`@/assets/img/${produit.photo}`)"  />
        </td>
          <td class="align-middle">{{ produit.nom }}</td>
          <td class="align-middle">{{ produit.description }}</td>
          <td class="align-middle">{{ (produit.prix ).toFixed(2)}} CAD</td>
          <td class="align-middle">{{ produit.categorie }}</td>
          <td class="align-middle">
            <button
              class="btn btn-primary btn-sm mr-2"
              data-toggle="modal"
              data-target="#ajouterProduitModal"
              @click="openModifierProduitModal(produit)"
            >
              Modifier
            </button>
            <button
              type="button"
              class="btn btn-danger btn-sm"
              data-bs-toggle="modal"
              data-bs-target="#staticBackdrop"
              @click="selectProduct(produit)"
            >
              Supprimer
            </button>
          </td>
        </tr>
      </tbody>
    </table> 
  </div>
  <!-- modal ajout -->
      <div class="modal fade" id="ajouterProduitModal" tabIndex="-1" role="dialog" aria-labelledby="ajouterProduitModalLabel" aria-hidden="true" >
        <div class="modal-dialog" role="document">
            <div class="modal-content">
            <div class="modal-header">
                <h5 class="modal-title" id="ajouterProduitModalLabel">{{ modalTitle }}</h5>
                <button type="button" class="close" data-dismiss="modal" aria-label="Fermer">
                <span aria-hidden="true">&times;</span>
                </button>
            </div>
            <div class="modal-body">
                <div class="form-group">
                    <label htmlFor="nom">Nom du produit</label>
                    <input type="text" class="form-control"  placeholder="Entrez le nom du produit" v-model="produit.nom"/>
                </div>
                <div class="form-group">
                    <label htmlFor="description">Description du produit</label>
                    <textarea class="form-control"  rows="3" placeholder="Entrez la description du produit" v-model="produit.description"></textarea>
                </div>
                <div class="form-group">
                    <label htmlFor="prix">Prix du produit</label>
                    <input type="number" class="form-control"  placeholder="Entrez le prix du produit" v-model.number="produit.prix"/>
                </div>
                <div class="form-group">
                    <label htmlFor="photo">Photo</label>
                    <input type="text" class="form-control"  placeholder="Entrez le nom de photo" v-model="produit.photo"/>
                </div>                
                <div class="form-group">
                    <label htmlFor="categorie">Catégorie du produit</label>
                    <select class="form-control" v-model="produit.categorie" >
                        <option value="">Sélectionnez une catégorie</option>
                        <option value="Smartphone">Smartphone</option>
                        <option value="Ordinateur">Ordinateur</option>
                        <option value="Accesoires">Accesoires</option>
                    </select>                    
                </div>
                            <div class="modal-footer">
                <button type="button" class="btn btn-secondary" data-dismiss="modal">Fermer</button>
                <input type="button" class="btn btn-primary" :value="`${modalButtonLabel}`" @click="saveProduct" data-dismiss="modal"/>

            </div>
                
            </div>
            </div>
        </div>
        </div>
        <!-- fin modal ajout -->
        <!-- modal suppression -->
      <div
        className="modal fade"
        id="staticBackdrop"
        data-bs-backdrop="static"
        data-bs-keyboard="false"
        tabIndex="-1"
        aria-labelledby="staticBackdropLabel"
        aria-hidden="true"
      >
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h1 class="modal-title fs-5" id="staticBackdropLabel">
                Suppression
              </h1>
              <button
                type="button"
                class="btn-close"
                data-bs-dismiss="modal"
                aria-label="Close"
              ></button>
            </div>
            <div class="modal-body">
              Êtes-vous sûr de vouloir supprimer cette ligne ?
            </div>
            <div class="modal-footer">
              <button
                type="button"
                class="btn btn-secondary"
                data-bs-dismiss="modal"
              >
                Fermer
              </button>
              <button
                data-bs-dismiss="modal"
                class="btn btn-danger btn-sm"
                @click="deleteProduct"
              >
                Supprimer
              </button>
            </div>
          </div>
        </div>
      </div>        
        <!-- fin modal suppression -->
</template>

<script>
import ProductDataService from '@/services/ProductDataService'

export default {
  props: ['produits','ajouterProduit', 'supprimerProduit','modifierProduit'],
    data () {
    return {
      produit: {
        nom: null,
        description: null,
        prix: null,
        photo: null,
        categorie: null
      },
      produitSelectionne:null,
      modalTitle:"Ajouter article",
      modalButtonLabel :"Ajouter",
      message: null,
    }
  },
   methods: {
    saveProduct() {
        if (this.produitSelectionne) {
        this.editProduct();
        } else {
        this.createProduct();
        }
    },    
    createProduct() {
    ProductDataService.create(this.produit)
      .then(response => {
        this.produit.id = response.data.id;
        this.ajouterProduit(this.produit);
        this.message = response.data.message
        this.resetForm();
      });
    },
    editProduct() {
    ProductDataService.update(this.produitSelectionne.id, this.produit)
      .then(response => {
        const index = this.productIndex();
        this.modifierProduit(index, this.produit);
        this.message = response.data.message
        this.resetForm();
      });
    },
    resetForm() {
        this.produit = {
        nom: null,
        description: null,
        prix: null,
        photo: null,
        categorie: null
        };
        this.produitSelectionne = null;
    },       
    selectProduct (selectProduit) {
     this.produitSelectionne = selectProduit 
    },
    deleteProduct() {
        ProductDataService.delete(this.produitSelectionne.id)
            .then(response => {
            const index = this.productIndex();
            this.supprimerProduit(index);
            this.message = response.data.message
            this.$router.push({ name: 'product' });
            });
    },
    
    productIndex () {
    const index = this.produits.findIndex((elemnt) => {
        return elemnt.id === this.produitSelectionne.id
    })
     return index 
    }
    ,    
    newProduct () {
      this.produit = {}
    }, 
    openAjouterProduitModal() {
        this.titleModalAjouter();
        this.newProduct();
     },
    openModifierProduitModal(produit) {
        this.titleModalModifier();
        this.selectProduct(produit);
         this.newProduct();
        this.produit = this.produitSelectionne
     },     
    titleModalAjouter () {
     this.modalTitle = "Ajouter article"
     this.modalButtonLabel = "Ajouter"
    },
     titleModalModifier () {
     this.modalTitle = "Modifier article"
     this.modalButtonLabel = "Sauvegarder"
    }
  }
}
</script>



