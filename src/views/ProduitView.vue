<template>


  <main>

    <div>

      <table>
        <caption>Produits - page {{ data.page.number + 1 }} / {{ data.page.totalPages }} </caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commande</th>
        </tr>
        <!-- tableau produit vide -->
        <tr v-if="data.listeProduits.length === 0">
          <td colspan="4">Produits en cours de chargement ...</td>
        </tr>
        <!-- tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>

      </table>

      <div class="pagination">

        <button @click="chargementProduits(0)">
          Début
        </button>

        <button @click="data.page.number + 1 > 1 ? chargementProduits(data.page.number - 1) : ''">
          Précédent
        </button>

        <button @click="data.page.number + 1 < data.page.totalPages ? chargementProduits(data.page.number + 1) : ''">
          Suivant
        </button>

        <button @click="chargementProduits(data.page.totalPages - 1)">
          Fin
        </button>

      </div>

    </div>

  </main>
  
</template>



<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest, APIError } from "../api";
let data = reactive({
  // table de la liste des produits affichés
  listeProduits: [],
  // Infos de la page
  page: {}
});


function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}


// récupération de la liste des catégories via l'API
// Trie ascendant par code catégorie
//Enregistre les produits dans un tableau d'une page passée en paramètre
//@param {number} nPage Numéro de la page
function chargementProduits(nPage) {
  doAjaxRequest(`/api/produits?page=${nPage}&size=5&sort=nom,asc`)
      .then((json) => {
        data.listeProduits = json._embedded.produits;
        data.page = json.page;
      })
      .catch(showError);
}
// A l'affichage de la liste des produits de la page n°1
onMounted(() => {
  chargementProduits(0);
});
</script>


<style scoped>
body {
  font-family: 'Courier New', Courier, monospace;
}

table {
  border-collapse: collapse;
  width: 100%;
}

th, td {
  text-align: left;
  padding: 8px;
}

tr:nth-child(even) {
  background-color: #777777;
}

th {
  background-color: #ffcc00;
  color: white;
}

button {
  background-color: #ffcc00;
  color: white;
  border: none;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
}
</style>