<template>
    <div class="filter-bar">
      <div class="search-box">
        <input 
          type="text" 
          v-model="searchQuery" 
          placeholder="Buscar produtos..."
          @input="updateSearch"
        >
      </div>
  
      <div class="category-filter">
        <select v-model="selectedCategory" @change="updateCategory">
          <option value="">Todas as categorias</option>
          <option v-for="category in categories" :key="category" :value="category">
            {{ category }}
          </option>
        </select>
      </div>
  
      <div class="price-filter">
        <input 
          type="number" 
          v-model="minPrice" 
          placeholder="Preço mínimo"
          @input="updatePrice"
        >
        <input 
          type="number" 
          v-model="maxPrice" 
          placeholder="Preço máximo"
          @input="updatePrice"
        >
      </div>
  
      <div class="stock-filter">
        <label>
          <input 
            type="checkbox" 
            v-model="inStock"
            @change="updateStock"
          >
          Apenas produtos em estoque
        </label>
      </div>
  
      <button class="clear-filters" @click="clearAllFilters">
        Limpar Filtros
      </button>
    </div>
  </template>
  
  <script>
  export default {
    name: 'FilterBar',
    data() {
      return {
        searchQuery: '',
        selectedCategory: '',
        minPrice: '',
        maxPrice: '',
        inStock: false
      }
    },
    computed: {
      categories() {
        return this.$store.state.categories
      }
    },
    methods: {
      updateSearch() {
        this.$store.commit('setFilter', {
          type: 'searchQuery',
          value: this.searchQuery
        })
      },
      updateCategory() {
        this.$store.commit('setFilter', {
          type: 'category',
          value: this.selectedCategory
        })
      },
      updatePrice() {
        this.$store.commit('setFilter', {
          type: 'minPrice',
          value: this.minPrice ? Number(this.minPrice) : null
        })
        this.$store.commit('setFilter', {
          type: 'maxPrice',
          value: this.maxPrice ? Number(this.maxPrice) : null
        })
      },
      updateStock() {
        this.$store.commit('setFilter', {
          type: 'inStock',
          value: this.inStock
        })
      },
      clearAllFilters() {
        this.$store.commit('clearFilters')
        this.searchQuery = ''
        this.selectedCategory = ''
        this.minPrice = ''
        this.maxPrice = ''
        this.inStock = false
      }
    }
  }
  </script>
  
  <style>
  .filter-bar {
    background: #f5f5f5;
    padding: 20px;
    border-radius: 8px;
    margin-bottom: 20px;
    display: flex;
    gap: 20px;
    flex-wrap: wrap;
  }
  
  .search-box input,
  .category-filter select,
  .price-filter input {
    padding: 8px;
    border: 1px solid #ddd;
    border-radius: 4px;
    width: 200px;
  }
  
  .price-filter {
    display: flex;
    gap: 10px;
  }
  
  .price-filter input {
    width: 120px;
  }
  
  .clear-filters {
    background: #ff4444;
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 4px;
    cursor: pointer;
  }
  
  .clear-filters:hover {
    background: #cc0000;
  }
  </style>