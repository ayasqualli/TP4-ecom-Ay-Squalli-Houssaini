<template>
  <div id="app">
    <header>
      <img src="images/logo-les-bonnes-pieces.png" alt="LOGO" />
      <h1>Les Bonnes Pièces</h1>
    </header>
    <main>
      <section class="filtres">
        <h3>Filtres</h3>
        <input v-model="searchQuery" placeholder="Rechercher..." />
        <select v-model="selectedCategory">
          <option value="">Toutes les catégories</option>
          <option v-for="category in categories" :key="category">
            {{ category }}
          </option>
        </select>
        <label>
          <input type="checkbox" v-model="showAvailableOnly" /> Disponible
          seulement
        </label>
        <button @click="sortByPrice('asc')">Trier par prix croissant</button>
        <button @click="sortByPrice('desc')">Trier par prix décroissant</button>
      </section>
      <section class="fiches">
        <div v-if="filteredPieces.length === 0">Aucun résultat trouvé</div>
        <div v-else v-for="piece in filteredPieces" :key="piece.id" class="fiche">
          <img :src="piece.image" :alt="piece.nom" />
          <h3>{{ piece.nom }}</h3>
          <p>{{ piece.prix }} €</p>
          <p>{{ piece.categorie }}</p>
          <button @click="addToCart(piece)" :disabled="!piece.disponible">
            Ajouter au panier
          </button>
        </div>
      </section>
      <section class="panier">
        <h3>Panier</h3>
        <ul>
          <li v-for="item in cart" :key="item.id">
            {{ item.nom }} - {{ item.prix }} €
            <button @click="removeFromCart(item.id)" class="delete-btn">×</button>
          </li>
        </ul>
      </section>
    </main>
  </div>
</template>

<script>
export default {
  data() {
    return {
      pieces: [
        {
          id: 1,
          nom: "Batterie 12V",
          prix: 120,
          categorie: "Électricité",
          image: "https://placehold.co/200x200/FFD700/000000?text=Batterie+12V&font-size=16",
          disponible: true,
        },
        {
          id: 2,
          nom: "Filtre à huile",
          prix: 15,
          categorie: "Filtration",
          image: "https://placehold.co/200x200/FFA07A/000000?text=Filtre+%C3%A0+huile&font-size=16",
          disponible: true,
        },
        {
          id: 3,
          nom: "Bougies d'allumage (x4)",
          prix: 35,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Bougies+d%27allumage&font-size=16",
          disponible: true,
        },
        {
          id: 4,
          nom: "Disques de frein (x2)",
          prix: 80,
          categorie: "Freinage",
          image:
            "https://placehold.co/200x200/DC143C/FFFFFF?text=Disques+de+frein&font-size=16",
          disponible: true,
        },
        {
          id: 5,
          nom: "Courroie de distribution",
          prix: 90,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Courroie+de+distribution&font-size=16",
          disponible: true,
        },
        {
          id: 6,
          nom: "Pompe à eau",
          prix: 70,
          categorie: "Refroidissement",
          image:
            "https://placehold.co/200x200/00BFFF/000000?text=Pompe+%C3%A0+eau&font-size=16",
          disponible: true,
        },
        {
          id: 7,
          nom: "Amortisseurs arrière (x2)",
          prix: 150,
          categorie: "Suspension",
          image:
            "https://placehold.co/200x200/8A2BE2/FFFFFF?text=Amortisseurs+arri%C3%A8re&font-size=16",
          disponible: true,
        },
        {
          id: 8,
          nom: "Filtre à air",
          prix: 20,
          categorie: "Filtration",
          image:
            "https://placehold.co/200x200/FFA07A/000000?text=Filtre+%C3%A0+air&font-size=16",
          disponible: true,
        },
        {
          id: 9,
          nom: "Capteur ABS",
          prix: 50,
          categorie: "Sécurité",
          image:
            "https://placehold.co/200x200/FF4500/FFFFFF?text=Capteur+ABS&font-size=16",
          disponible: true,
        },
        {
          id: 10,
          nom: "Radiateur de refroidissement",
          prix: 130,
          categorie: "Refroidissement",
          image:
            "https://placehold.co/200x200/00BFFF/000000?text=Radiateur+de+refroidissement&font-size=16",
          disponible: true,
        },
        {
          id: 11,
          nom: "Alternateur",
          prix: 200,
          categorie: "Électricité",
          image:
            "https://placehold.co/200x200/FFD700/000000?text=Alternateur&font-size=16",
          disponible: true,
        },
        {
          id: 12,
          nom: "Démarreur",
          prix: 180,
          categorie: "Électricité",
          image:
            "https://placehold.co/200x200/FFD700/000000?text=D%C3%A9marreur&font-size=16",
          disponible: true,
        },
        {
          id: 13,
          nom: "Kit d'embrayage",
          prix: 220,
          categorie: "Transmission",
          image:
            "https://placehold.co/200x200/FF8C00/000000?text=Kit+d%27embrayage&font-size=16",
          disponible: true,
        },
        {
          id: 14,
          nom: "Injecteur de carburant",
          prix: 110,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Injecteur+de+carburant&font-size=16",
          disponible: true,
        },
        {
          id: 15,
          nom: "Pompe à carburant",
          prix: 90,
          categorie: "Carburant",
          image:
            "https://placehold.co/200x200/FFD700/000000?text=Pompe+%C3%A0+carburant&font-size=16",
          disponible: true,
        },
        {
          id: 16,
          nom: "Capteur de pression des pneus (TPMS)",
          prix: 45,
          categorie: "Sécurité",
          image:
            "https://placehold.co/200x200/FF4500/FFFFFF?text=Capteur+TPMS&font-size=16",
          disponible: true,
        },
        {
          id: 17,
          nom: "Rétroviseur extérieur",
          prix: 60,
          categorie: "Carrosserie",
          image:
            "https://placehold.co/200x200/20B2AA/000000?text=R%C3%A9troviseur+ext%C3%A9rieur&font-size=16",
          disponible: true,
        },
        {
          id: 18,
          nom: "Échappement complet",
          prix: 250,
          categorie: "Échappement",
          image:
            "https://placehold.co/200x200/808080/FFFFFF?text=%C3%89chappement+complet&font-size=16",
          disponible: true,
        },
        {
          id: 19,
          nom: "Vanne EGR",
          prix: 140,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Vanne+EGR&font-size=16",
          disponible: true,
        },
        {
          id: 20,
          nom: "Turbo",
          prix: 400,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Turbo&font-size=16",
          disponible: true,
        },
        {
          id: 21,
          nom: "Joint de culasse",
          prix: 75,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Joint+de+culasse&font-size=16",
          disponible: true,
        },
        {
          id: 22,
          nom: "Boîtier de direction",
          prix: 300,
          categorie: "Direction",
          image:
            "https://placehold.co/200x200/FF8C00/000000?text=Bo%C3%AEtier+de+direction&font-size=16",
          disponible: true,
        },
        {
          id: 23,
          nom: "Silent bloc de suspension",
          prix: 35,
          categorie: "Suspension",
          image:
            "https://placehold.co/200x200/8A2BE2/FFFFFF?text=Silent+bloc+de+suspension&font-size=16",
          disponible: true,
        },
        {
          id: 24,
          nom: "Cardan de transmission",
          prix: 160,
          categorie: "Transmission",
          image:
            "https://placehold.co/200x200/FF8C00/000000?text=Cardan+de+transmission&font-size=16",
          disponible: true,
        },
        {
          id: 25,
          nom: "Capteur de position vilebrequin",
          prix: 50,
          categorie: "Moteur",
          image:
            "https://placehold.co/200x200/FF6347/000000?text=Capteur+de+position+vilebrequin&font-size=16",
          disponible: true,
        },
      ],
      searchQuery: "",
      selectedCategory: "",
      showAvailableOnly: false,
      cart: [],
      sortOrder: "asc"
    };
  },
  methods: {
    addToCart(piece) {
      if (piece.disponible) {
        this.cart.push(piece);
      }
    },
    removeFromCart(pieceId) {
      this.cart = this.cart.filter(item => item.id !== pieceId);
    },
    sortByPrice(order) {
      this.sortOrder = order;
    }
  },
  computed: {
    categories() {
      return [...new Set(this.pieces.map((piece) => piece.categorie))];
    },
    filteredPieces() {
      let filtered = this.pieces;
      if (this.searchQuery) {
        filtered = filtered.filter((piece) =>
          piece.nom.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      }
      if (this.selectedCategory) {
        filtered = filtered.filter(
          (piece) => piece.categorie === this.selectedCategory
        );
      }
      if (this.showAvailableOnly) {
        filtered = filtered.filter((piece) => piece.disponible);
      }
      if (this.sortOrder === "asc") {
        filtered.sort((a, b) => a.prix - b.prix);
      } else {
        filtered.sort((a, b) => b.prix - a.prix);
      }
      return filtered;
    },
  },
};
</script>
