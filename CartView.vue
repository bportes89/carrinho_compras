<template>
    <div class="cart">
      <h1>Carrinho de Compras</h1>
      <div v-if="cart.length === 0" class="empty-cart">
        Seu carrinho está vazio
      </div>
      <div v-else>
        <div class="cart-items">
          <div v-for="item in cart" :key="item.id" class="cart-item">
            <img :src="item.image" :alt="item.name">
            <div class="item-details">
              <h3>{{ item.name }}</h3>
              <p class="description">{{ item.description }}</p>
              <div class="quantity-controls">
                <button @click="updateQuantity(item.id, item.quantity - 1)" :disabled="item.quantity <= 1">-</button>
                <span>{{ item.quantity }}</span>
                <button @click="updateQuantity(item.id, item.quantity + 1)" :disabled="item.quantity >= item.stock">+</button>
              </div>
              <p class="price">R$ {{ (item.price * item.quantity).toFixed(2) }}</p>
              <button @click="removeItem(item.id)" class="remove-btn">Remover</button>
            </div>
          </div>
        </div>
        <div class="cart-summary">
          <h3>Resumo do Pedido</h3>
          <p>Total de Itens: {{ cartItemsCount }}</p>
          <p class="total">Total: R$ {{ cartTotal.toFixed(2) }}</p>
          <button class="checkout-btn">Finalizar Compra</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: 'CartView',
    computed: {
      cart() {
        return this.$store.state.cart
      },
      cartTotal() {
        return this.$store.getters.cartTotal
      },
      cartItemsCount() {
        return this.$store.getters.cartItemsCount
      }
    },
    methods: {
      removeItem(productId) {
        this.$store.commit('removeFromCart', productId)
      },
      updateQuantity(productId, quantity) {
        this.$store.commit('updateCartItemQuantity', { productId, quantity })
      }
    }
  }
  </script>
  
  <style>
  .cart-summary {
    background: #f5f5f5;
    padding: 20px;
    border-radius: 8px;
    margin-top: 20px;
  }
  
  .total {
    font-size: 1.5em;
    font-weight: bold;
    color: #42b983;
  }
  
  .checkout-btn {
    background: #42b983;
    color: white;
    border: none;
    padding: 15px 30px;
    border-radius: 4px;
    width: 100%;
    margin-top: 20px;
    font-size: 1.1em;
    cursor: pointer;
  }
  
  .checkout-btn:hover {
    background: #3aa876;
  }
  
  .quantity-controls {
    display: flex;
    align-items: center;
    gap: 10px;
    margin: 10px 0;
  }
  
  .quantity-controls button {
    width: 30px;
    height: 30px;
    border-radius: 50%;
    border: 1px solid #ddd;
    background: white;
    cursor: pointer;
  }
  
  .quantity-controls button:disabled {
    opacity: 0.5;
    cursor: not-allowed;
  }
  </style>