<template>
  <div class="home">
    <FilterBar />
    
    <div class="view-controls">
      <div class="sort-controls">
        <select v-model="sortBy" @change="sortProducts">
          <option value="name">Nome</option>
          <option value="price-asc">Menor Preço</option>
          <option value="price-desc">Maior Preço</option>
          <option value="rating">Melhor Avaliação</option>
        </select>
      </div>

      <div class="view-toggle">
        <button :class="{ active: viewMode === 'grid' }" @click="viewMode = 'grid'">
          <i class="fas fa-th"></i> Grade
        </button>
        <button :class="{ active: viewMode === 'list' }" @click="viewMode = 'list'">
          <i class="fas fa-list"></i> Lista
        </button>
      </div>

      <div class="results-count">
        Mostrando {{ filteredProducts.length }} produtos
      </div>
    </div>

    <div :class="['products-container', viewMode]">
      <ProductCard
        v-for="product in sortedProducts"
        :key="product.id"
        :product="product"
        :view-mode="viewMode"
      />
    </div>

    <div v-if="filteredProducts.length === 0" class="no-results">
      <h3>Nenhum produto encontrado</h3>
      <p>Tente ajustar seus filtros de busca</p>
    </div>

    <div class="pagination" v-if="totalPages > 1">
      <button 
        @click="currentPage--" 
        :disabled="currentPage === 1"
      >Anterior</button>
      <span>{{ currentPage }} de {{ totalPages }}</span>
      <button 
        @click="currentPage++" 
        :disabled="currentPage === totalPages"
      >Próxima</button>
    </div>
  </div>
</template>

<script>
import ProductCard from '@/components/ProductCard.vue'
import FilterBar from '@/components/FilterBar.vue'

export default {
  name: 'HomeView',
  components: {
    ProductCard,
    FilterBar
  },
  data() {
    return {
      sortBy: 'name',
      viewMode: 'grid',
      currentPage: 1,
      itemsPerPage: 6
    }
  },
  computed: {
    filteredProducts() {
      return this.$store.getters.filteredProducts
    },
    sortedProducts() {
      const products = [...this.filteredProducts]
      switch(this.sortBy) {
        case 'price-asc':
          return products.sort((a, b) => a.price - b.price)
        case 'price-desc':
          return products.sort((a, b) => b.price - a.price)
        case 'rating':
          return products.sort((a, b) => b.rating - a.rating)
        default:
          return products.sort((a, b) => a.name.localeCompare(b.name))
      }
    },
    totalPages() {
      return Math.ceil(this.filteredProducts.length / this.itemsPerPage)
    }
  },
  methods: {
    sortProducts() {
      // Método mantido para possíveis extensões futuras
    }
  },
  watch: {
    'filters': {
      handler() {
        this.currentPage = 1
      },
      deep: true
    }
  }
}
</script>

<style>
.view-controls {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin: 20px 0;
  padding: 10px;
  background: #f5f5f5;
  border-radius: 8px;
}

.view-toggle button {
  padding: 8px 16px;
  margin: 0 5px;
  border: 1px solid #ddd;
  background: white;
  cursor: pointer;
}

.view-toggle button.active {
  background: #42b983;
  color: white;
  border-color: #42b983;
}

.products-container.grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 20px;
}

.products-container.list {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.no-results {
  text-align: center;
  padding: 40px;
  color: #666;
}

.pagination {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 20px;
  margin-top: 30px;
}

.pagination button {
  padding: 8px 16px;
  border: 1px solid #42b983;
  background: white;
  color: #42b983;
  cursor: pointer;
}

.pagination button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.results-count {
  color: #666;
  font-size: 0.9em;
}

.sort-controls select {
  padding: 8px;
  border: 1px solid #ddd;
  border-radius: 4px;
}
</style>