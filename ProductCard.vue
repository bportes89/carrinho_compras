<template>
  <div :class="['product-card', viewMode]">
    <div class="product-image">
      <img :src="product.image" :alt="product.name">
      <button class="favorite-btn" @click="toggleFavorite">
        <i :class="['fas', isFavorite ? 'fa-heart' : 'fa-heart-o']"></i>
      </button>
    </div>
    
    <div class="product-info">
      <h3>{{ product.name }}</h3>
      <div class="rating">
        ★★★★★
        <span :style="{ width: (product.rating * 20) + '%' }" class="rating-filled">★★★★★</span>
        <span class="rating-count">({{ product.reviews }})</span>
      </div>
      
      <p class="description">{{ product.description }}</p>
      
      <ul class="specs" v-if="viewMode === 'list'">
        <li v-for="(spec, index) in product.specs" :key="index">{{ spec }}</li>
      </ul>
      
      <div class="price-section">
        <p class="price">
          <span class="original-price" v-if="product.originalPrice">
            R$ {{ product.originalPrice.toFixed(2) }}
          </span>
          R$ {{ product.price.toFixed(2) }}
        </p>
        <span class="discount-badge" v-if="product.originalPrice">
          {{ calculateDiscount }}% OFF
        </span>
      </div>

      <div class="stock-info">
        <span :class="['stock-badge', { 'low-stock': product.stock < 5 }]">
          {{ product.stock }} em estoque
        </span>
      </div>

      <div class="actions">
        <div class="quantity-selector">
          <button @click="decreaseQuantity" :disabled="quantity <= 1">-</button>
          <span>{{ quantity }}</span>
          <button @click="increaseQuantity" :disabled="quantity >= product.stock">+</button>
        </div>
        <button 
          class="add-cart-btn" 
          @click="addToCart" 
          :disabled="product.stock === 0"
        >
          Adicionar ao Carrinho
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductCard',
  props: {
    product: {
      type: Object,
      required: true
    },
    viewMode: {
      type: String,
      default: 'grid'
    }
  },
  data() {
    return {
      quantity: 1
    }
  },
  computed: {
    isFavorite() {
      return this.$store.getters.isFavorite(this.product.id)
    },
    calculateDiscount() {
      if (!this.product.originalPrice) return 0
      return Math.round(
        ((this.product.originalPrice - this.product.price) / this.product.originalPrice) * 100
      )
    }
  },
  methods: {
    addToCart() {
      this.$store.commit('addToCart', {
        ...this.product,
        quantity: this.quantity
      })
      this.quantity = 1
    },
    decreaseQuantity() {
      if (this.quantity > 1) this.quantity--
    },
    increaseQuantity() {
      if (this.quantity < this.product.stock) this.quantity++
    },
    toggleFavorite() {
      this.$store.commit('toggleFavorite', this.product.id)
    }
  }
}
</script>

<style>
.product-card {
  position: relative;
  border: 1px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
  transition: transform 0.2s, box-shadow 0.2s;
}

.product-card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}

.product-card.grid {
  display: flex;
  flex-direction: column;
  padding: 15px;
}

.product-card.list {
  display: flex;
  padding: 20px;
}

.product-card.list .product-image {
  flex: 0 0 200px;
  margin-right: 20px;
}

.product-card.list .product-info {
  flex: 1;
  text-align: left;
}

.favorite-btn {
  position: absolute;
  top: 10px;
  right: 10px;
  background: white;
  border: none;
  border-radius: 50%;
  width: 30px;
  height: 30px;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
}

.rating {
  position: relative;
  display: inline-block;
  color: #ddd;
  font-size: 20px;
}

.rating-filled {
  position: absolute;
  top: 0;
  left: 0;
  color: #ffd700;
  overflow: hidden;
}

.price-section {
  display: flex;
  align-items: center;
  gap: 10px;
  margin: 10px 0;
}

.discount-badge {
  background: #ff4444;
  color: white;
  padding: 3px 8px;
  border-radius: 12px;
  font-size: 0.8em;
}

.actions {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-top: 15px;
}

.quantity-selector {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.quantity-selector button {
  width: 30px;
  height: 30px;
  border-radius: 50%;
  border: 1px solid #ddd;
  background: white;
  cursor: pointer;
}

.add-cart-btn {
  background: #42b983;
  color: white;
  border: none;
  padding: 10px;
  border-radius: 4px;
  cursor: pointer;
  width: 100%;
}

.add-cart-btn:disabled {
  background: #cccccc;
  cursor: not-allowed;
}

.stock-badge {
  display: inline-block;
  padding: 3px 8px;
  border-radius: 12px;
  background: #42b983;
  color: white;
  font-size: 0.8em;
}

.stock-badge.low-stock {
  background: #ff4444;
}
</style>