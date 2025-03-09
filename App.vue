<template>
  <div id="app" :class="{ 'dark-mode': isDarkMode }">
    <div class="notifications" v-if="notifications.length">
      <div v-for="notification in notifications" 
           :key="notification.id" 
           :class="['notification', notification.type]">
        {{ notification.message }}
      </div>
    </div>

    <header class="glass">
      <nav class="nav-container">
        <div class="logo" @click="$router.push('/')">
          <div class="logo-animation">
            <i class="fas fa-shopping-bag"></i>
          </div>
          <span>TechStore</span>
        </div>

        <div class="search-wrapper">
          <div class="search-bar">
            <i class="fas fa-search"></i>
            <input 
              type="text" 
              placeholder="Buscar produtos..." 
              v-model="searchQuery" 
              @input="handleSearch"
              @focus="showSuggestions = true"
            >
          </div>
          <div class="search-suggestions" v-if="showSuggestions && filteredProducts.length">
            <div v-for="product in filteredProducts.slice(0, 5)" 
                 :key="product.id"
                 class="suggestion-item"
                 @click="goToProduct(product)">
              <img :src="product.image" :alt="product.name">
              <div>
                <h4>{{ product.name }}</h4>
                <p>R$ {{ product.price.toFixed(2) }}</p>
              </div>
            </div>
          </div>
        </div>

        <div class="nav-actions">
          <div class="nav-links">
            <router-link to="/" class="nav-link" title="Home">
              <i class="fas fa-home"></i>
              <span>Home</span>
            </router-link>
            
            <router-link to="/categories" class="nav-link" title="Categorias">
              <i class="fas fa-th-large"></i>
              <span>Categorias</span>
            </router-link>

            <router-link to="/favorites" class="nav-link" title="Favoritos">
              <i class="fas fa-heart"></i>
              <span>Favoritos</span>
              <div v-if="favoritesCount" class="badge">{{ favoritesCount }}</div>
            </router-link>
          </div>

          <div class="cart-wrapper">
            <router-link to="/cart" class="cart-button" title="Carrinho">
              <i class="fas fa-shopping-cart"></i>
              <span>Carrinho</span>
              <div v-if="cartItemsCount" class="badge pulse">{{ cartItemsCount }}</div>
            </router-link>
            <div class="cart-preview" v-if="cartItems.length">
              <div class="preview-items">
                <div v-for="item in cartItems.slice(0, 3)" 
                     :key="item.id" 
                     class="preview-item">
                  <img :src="item.image" :alt="item.name">
                  <div class="item-details">
                    <h4>{{ item.name }}</h4>
                    <p>{{ item.quantity }}x R$ {{ item.price.toFixed(2) }}</p>
                  </div>
                </div>
              </div>
              <div class="preview-total">
                <span>Total: R$ {{ cartTotal.toFixed(2) }}</span>
                <router-link to="/cart" class="checkout-button">
                  Finalizar Compra
                </router-link>
              </div>
            </div>
          </div>

          <button class="theme-toggle" @click="toggleTheme">
            <i :class="['fas', isDarkMode ? 'fa-sun' : 'fa-moon']"></i>
          </button>
        </div>
      </nav>
    </header>

    <main>
      <transition-group name="page" mode="out-in">
        <router-view :key="$route.fullPath"/>
      </transition-group>
    </main>

    <footer class="glass">
      <div class="footer-content">
        <div class="footer-section brand">
          <h3>
            <i class="fas fa-shopping-bag"></i>
            TechStore
          </h3>
          <p>Sua loja de tecnologia favorita</p>
          <div class="newsletter">
            <h4>Receba nossas novidades</h4>
            <div class="newsletter-form">
              <input 
                type="email" 
                v-model="email" 
                placeholder="Seu melhor e-mail"
              >
              <button @click="subscribe">Assinar</button>
            </div>
          </div>
        </div>

        <div class="footer-section links">
          <h4>Links Úteis</h4>
          <router-link to="/">Home</router-link>
          <router-link to="/categories">Categorias</router-link>
          <router-link to="/about">Sobre</router-link>
          <router-link to="/contact">Contato</router-link>
        </div>

        <div class="footer-section contact">
          <h4>Contato</h4>
          <p>
            <i class="fas fa-phone"></i>
            0800 123 4567
          </p>
          <p>
            <i class="fas fa-envelope"></i>
            contato@techstore.com
          </p>
          <p>
            <i class="fas fa-map-marker-alt"></i>
            Rua Tecnologia, 123
          </p>
        </div>

        <div class="footer-section social">
          <h4>Redes Sociais</h4>
          <div class="social-links">
            <a href="#" target="_blank" title="Instagram">
              <i class="fab fa-instagram"></i>
            </a>
            <a href="#" target="_blank" title="Facebook">
              <i class="fab fa-facebook"></i>
            </a>
            <a href="#" target="_blank" title="Twitter">
              <i class="fab fa-twitter"></i>
            </a>
            <a href="#" target="_blank" title="YouTube">
              <i class="fab fa-youtube"></i>
            </a>
          </div>
        </div>
      </div>

      <div class="footer-bottom">
        <p>&copy; 2024 TechStore. Todos os direitos reservados.</p>
      </div>
    </footer>

    <button 
      v-show="showScrollTop" 
      class="scroll-top" 
      @click="scrollToTop"
      title="Voltar ao topo"
    >
      <i class="fas fa-arrow-up"></i>
    </button>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      isDarkMode: false,
      searchQuery: '',
      showScrollTop: false,
      showSuggestions: false,
      email: '',
      notifications: []
    }
  },
  computed: {
    cartItemsCount() {
      return this.$store.getters.cartItemsCount || 0
    },
    cartItems() {
      return this.$store.state.cart || []
    },
    cartTotal() {
      return this.$store.getters.cartTotal || 0
    },
    favoritesCount() {
      return this.$store.state.favorites?.length || 0
    },
    filteredProducts() {
      if (!this.searchQuery) return []
      return this.$store.getters.searchProducts(this.searchQuery) || []
    }
  },
  methods: {
    toggleTheme() {
      this.isDarkMode = !this.isDarkMode
      localStorage.setItem('darkMode', this.isDarkMode)
      document.documentElement.classList.toggle('dark-mode')
    },
    handleSearch: debounce(function() {
      this.$store.dispatch('searchProducts', this.searchQuery)
    }, 300),
    goToProduct(product) {
      this.$router.push(`/product/${product.id}`)
      this.showSuggestions = false
    },
    scrollToTop() {
      window.scrollTo({ top: 0, behavior: 'smooth' })
    },
    handleScroll() {
      this.showScrollTop = window.scrollY > 300
      if (window.scrollY > 50) {
        document.querySelector('header').classList.add('header-shadow')
      } else {
        document.querySelector('header').classList.remove('header-shadow')
      }
    },
    subscribe() {
      if (!this.email) {
        this.showNotification('Por favor, insira um e-mail válido', 'error')
        return
      }
      this.showNotification('Inscrição realizada com sucesso!', 'success')
      this.email = ''
    },
    showNotification(message, type = 'info') {
      const notification = {
        id: Date.now(),
        message,
        type
      }
      this.notifications.push(notification)
      setTimeout(() => {
        this.notifications = this.notifications.filter(n => n.id !== notification.id)
      }, 3000)
    }
  },
  mounted() {
    this.isDarkMode = localStorage.getItem('darkMode') === 'true'
    if (this.isDarkMode) {
      document.documentElement.classList.add('dark-mode')
    }
    window.addEventListener('scroll', this.handleScroll)
    document.addEventListener('click', (e) => {
      if (!e.target.closest('.search-wrapper')) {
        this.showSuggestions = false
      }
    })
  },
  beforeUnmount() {
    window.removeEventListener('scroll', this.handleScroll)
  }
}

function debounce(fn, delay) {
  let timeoutId
  return function (...args) {
    clearTimeout(timeoutId)
    timeoutId = setTimeout(() => fn.apply(this, args), delay)
  }
}
</script>

<style>
:root {
  --primary: #6366f1;
  --primary-dark: #4f46e5;
  --secondary: #3b82f6;
  --success: #10b981;
  --error: #ef4444;
  --warning: #f59e0b;
  --info: #3b82f6;
  
  --bg-light: #f8fafc;
  --bg-dark: #0f172a;
  --text-light: #1e293b;
  --text-dark: #e2e8f0;
  --card-light: #ffffff;
  --card-dark: #1e293b;
  
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
  
  --radius-sm: 0.375rem;
  --radius-md: 0.5rem;
  --radius-lg: 0.75rem;
  --radius-full: 9999px;
  
  --transition: all 0.3s ease;
}

/* Reset e estilos base */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', sans-serif;
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

#app {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: var(--bg-light);
  color: var(--text-light);
  transition: var(--transition);
}

.dark-mode {
  --bg-light: var(--bg-dark);
  --text-light: var(--text-dark);
  --card-light: var(--card-dark);
}

/* Header e Navegação */
header {
  position: sticky;
  top: 0;
  z-index: 50;
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(12px);
  transition: var(--transition);
}

.header-shadow {
  box-shadow: var(--shadow-md);
}

.nav-container {
  max-width: 1280px;
  margin: 0 auto;
  padding: 1rem;
  display: flex;
  align-items: center;
  gap: 2rem;
}

/* Logo */
.logo {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--primary);
  cursor: pointer;
}

.logo-animation {
  animation: float 3s ease-in-out infinite;
}

/* Barra de pesquisa */
.search-wrapper {
  position: relative;
  flex: 1;
  max-width: 600px;
}

.search-bar {
  position: relative;
}

.search-bar input {
  width: 100%;
  padding: 0.75rem 1rem 0.75rem 2.5rem;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: var(--radius-full);
  background: var(--card-light);
  color: var(--text-light);
  transition: var(--transition);
}

.search-bar i {
  position: absolute;
  left: 1rem;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-light);
  opacity: 0.5;
}

.search-suggestions {
  position: absolute;
  top: 100%;
  left: 0;
  right: 0;
  margin-top: 0.5rem;
  background: var(--card-light);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-lg);
  overflow: hidden;
  z-index: 40;
}

.suggestion-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.75rem;
  cursor: pointer;
  transition: var(--transition);
}

.suggestion-item:hover {
  background: rgba(0, 0, 0, 0.05);
}

.suggestion-item img {
  width: 40px;
  height: 40px;
  object-fit: cover;
  border-radius: var(--radius-sm);
}

/* Navegação e ações */
.nav-actions {
  display: flex;
  align-items: center;
  gap: 1.5rem;
}

.nav-links {
  display: flex;
  gap: 1rem;
}

.nav-link {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 1rem;
  color: var(--text-light);
  text-decoration: none;
  border-radius: var(--radius-md);
  transition: var(--transition);
}

.nav-link:hover {
  background: var(--primary);
  color: white;
}

/* Carrinho */
.cart-wrapper {
  position: relative;
}

.cart-button {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  padding: 0.5rem 1rem;
  color: var(--text-light);
  text-decoration: none;
  border-radius: var(--radius-md);
  transition: var(--transition);
}

.cart-button:hover {
  background: var(--primary);
  color: white;
}

.cart-preview {
  position: absolute;
  top: 100%;
  right: 0;
  width: 300px;
  background: var(--card-light);
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-lg);
  padding: 1rem;
  margin-top: 0.5rem;
  z-index: 100;
  opacity: 0;
  transform: translateY(-10px);
  pointer-events: none;
  transition: var(--transition);
}

.cart-wrapper:hover .cart-preview {
  opacity: 1;
  transform: translateY(0);
  pointer-events: auto;
}

.preview-items {
  max-height: 300px;
  overflow-y: auto;
}

.preview-item {
  display: flex;
  align-items: center;
  gap: 1rem;
  padding: 0.5rem;
  border-bottom: 1px solid rgba(0, 0, 0, 0.1);
}

.preview-item:last-child {
  border-bottom: none;
}

.preview-item img {
  width: 50px;
  height: 50px;
  object-fit: cover;
  border-radius: var(--radius-sm);
}

.item-details h4 {
  font-size: 0.875rem;
  margin-bottom: 0.25rem;
}

.item-details p {
  font-size: 0.75rem;
  color: var(--primary);
}

.preview-total {
  margin-top: 1rem;
  padding-top: 1rem;
  border-top: 1px solid rgba(0, 0, 0, 0.1);
  text-align: center;
}

.checkout-button {
  display: block;
  width: 100%;
  padding: 0.75rem;
  margin-top: 0.5rem;
  background: var(--primary);
  color: white;
  text-decoration: none;
  border-radius: var(--radius-md);
  transition: var(--transition);
}

.checkout-button:hover {
  background: var(--primary-dark);
}

/* Notificações */
.notifications {
  position: fixed;
  top: 1rem;
  right: 1rem;
  z-index: 1000;
  display: flex;
  flex-direction: column;
  gap: 0.5rem;
}

.notification {
  padding: 1rem;
  border-radius: var(--radius-md);
  color: white;
  animation: slideIn 0.3s ease;
}

.notification.success {
  background: var(--success);
}

.notification.error {
  background: var(--error);
}

.notification.info {
  background: var(--info);
}

/* Animações */
@keyframes slideIn {
  from {
    transform: translateX(100%);
    opacity: 0;
  }
  to {
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes float {
  0%, 100% {
    transform: translateY(0);
  }
  50% {
    transform: translateY(-5px);
  }
}

/* Footer */
footer {
  margin-top: auto;
  padding: 2rem 0 0;
  background: var(--card-light);
}

.footer-content {
  max-width: 1280px;
  margin: 0 auto;
  padding: 0 1rem;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 2rem;
}

.footer-section {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.footer-section h3 {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: var(--primary);
  font-size: 1.5rem;
}

.footer-section h4 {
  color: var(--text-light);
  margin-bottom: 0.5rem;
}

.footer-section a {
  color: var(--text-light);
  text-decoration: none;
  transition: var(--transition);
}

.footer-section a:hover {
  color: var(--primary);
}

.newsletter-form {
  display: flex;
  gap: 0.5rem;
}

.newsletter-form input {
  flex: 1;
  padding: 0.75rem;
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: var(--radius-md);
  background: var(--card-light);
  color: var(--text-light);
}

.newsletter-form button {
  padding: 0.75rem 1.5rem;
  background: var(--primary);
  color: white;
  border: none;
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: var(--transition);
}

.newsletter-form button:hover {
  background: var(--primary-dark);
}

.footer-section.contact p {
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.social-links {
  display: flex;
  gap: 1rem;
}

.social-links a {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background: var(--bg-light);
  color: var(--text-light);
  font-size: 1.25rem;
  transition: var(--transition);
}

.social-links a:hover {
  background: var(--primary);
  color: white;
  transform: translateY(-3px);
}

.footer-bottom {
  margin-top: 2rem;
  padding: 1.5rem;
  text-align: center;
  border-top: 1px solid rgba(0, 0, 0, 0.1);
}

/* Botão voltar ao topo */
.scroll-top {
  position: fixed;
  bottom: 2rem;
  right: 2rem;
  width: 45px;
  height: 45px;
  background: var(--primary);
  color: white;
  border: none;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: var(--transition);
  box-shadow: var(--shadow-lg);
  z-index: 40;
}

.scroll-top:hover {
  background: var(--primary-dark);
  transform: translateY(-3px);
}

/* Badge */
.badge {
  display: flex;
  align-items: center;
  justify-content: center;
  min-width: 20px;
  height: 20px;
  padding: 0 6px;
  background: var(--primary);
  color: white;
  border-radius: var(--radius-full);
  font-size: 0.75rem;
  font-weight: 600;
}

.badge.pulse {
  animation: pulse 2s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.1);
  }
  100% {
    transform: scale(1);
  }
}

/* Responsividade */
@media (max-width: 1024px) {
  .nav-container {
    padding: 1rem;
  }
  
  .search-wrapper {
    max-width: 400px;
  }
}

@media (max-width: 768px) {
  .nav-container {
    flex-direction: column;
    gap: 1rem;
  }

  .search-wrapper {
    width: 100%;
    max-width: none;
  }

  .nav-actions {
    width: 100%;
    justify-content: space-between;
  }

  .nav-links {
    flex-wrap: wrap;
    justify-content: center;
  }

  .nav-link span {
    display: none;
  }

  .cart-preview {
    position: fixed;
    top: auto;
    bottom: 0;
    left: 0;
    right: 0;
    width: 100%;
    margin: 0;
    border-radius: var(--radius-lg) var(--radius-lg) 0 0;
  }

  .footer-content {
    grid-template-columns: 1fr;
  }

  .newsletter-form {
    flex-direction: column;
  }
}

@media (max-width: 480px) {
  .nav-container {
    padding: 0.75rem;
  }

  .logo span {
    display: none;
  }

  .social-links {
    justify-content: center;
  }

  .scroll-top {
    bottom: 1rem;
    right: 1rem;
    width: 40px;
    height: 40px;
  }
}

/* Suporte para modo escuro */
.dark-mode header {
  background: rgba(15, 23, 42, 0.8);
}

.dark-mode .search-bar input {
  border-color: rgba(255, 255, 255, 0.1);
}

.dark-mode .suggestion-item:hover {
  background: rgba(255, 255, 255, 0.05);
}

.dark-mode footer {
  background: var(--card-dark);
}

.dark-mode .footer-bottom {
  border-color: rgba(255, 255, 255, 0.1);
}

.dark-mode .social-links a {
  background: var(--bg-dark);
}

/* Acessibilidade */
@media (prefers-reduced-motion: reduce) {
  * {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}

.visually-hidden {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
</style>
/* ... resto dos estilos permanecem iguais ... */