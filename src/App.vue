<script setup>
import { ref, computed, onMounted } from 'vue'

const index = ref(1)
const product = ref(null)
const loading = ref(false)
const category = ref('')

// Computed class for the page background
const pageClass = computed(() => {
  if (loading.value) return 'page-loading' // Optional or keep previous
  if (category.value === "men's clothing") return 'page-men-section'
  if (category.value === "women's clothing") return 'page-women-section'
  return 'page-unavailable'
})

// Fetch data logic
const fetchProduct = async () => {
  loading.value = true
  category.value = '' // Reset category while loading? Or keep? Better reset to avoid wrong bg
  product.value = null
  
  try {
    const res = await fetch(`https://fakestoreapi.com/products/${index.value}`)
    const data = await res.json()
    
    // Check category
    if (data.category === "men's clothing" || data.category === "women's clothing") {
      product.value = data
      category.value = data.category
    } else {
      // Unavailable
      product.value = null
      category.value = data.category // Need this for class binding "unavailable" (actually default is unavailable if not men/women, but strictly "section unavailable")
    }
  } catch (err) {
    console.error(err)
    category.value = 'unavailable' // Fallback
  } finally {
    loading.value = false
  }
}

const nextProduct = () => {
  index.value++
  if (index.value > 20) {
    index.value = 1
  }
  fetchProduct()
}

onMounted(() => {
  fetchProduct()
})
</script>

<template>
  <div class="page-container" :class="pageClass">
    
    <!-- Loader -->
    <div v-if="loading" class="loader-container">
       <div class="loader"></div>
    </div>

    <!-- Product Card -->
    <div v-else class="product-card">
      
      <!-- Unavailable View -->
      <div v-if="!product" class="unavailable-view">
        <div class="sad-face">☹</div>
        <div class="unavailable-msg">This product is unavailable to show</div>
        <button class="btn btn-next" @click="nextProduct">Next product</button>
      </div>

      <!-- Detail View -->
      <div v-else class="product-content">
        <div class="product-image">
          <img :src="product.image" :alt="product.title" />
        </div>
        
        <div class="product-details">
          <h2 class="title">{{ product.title }}</h2>
          
          <div class="sub-header">
            <span class="category">{{ product.category }}</span>
            <div class="rating">
               <!-- Simple rating display -->
               {{ product.rating?.rate }}/5
               <span class="rating-circles">
                 <span v-for="n in 5" :key="n" class="circle" 
                       :class="{ filled: n <= Math.round(product.rating?.rate || 0) }"></span>
               </span>
            </div>
          </div>
          
          <p class="description">{{ product.description }}</p>
          
          <div class="price">${{ product.price }}</div>
          
          <div class="actions">
            <button class="btn btn-buy">Buy now</button>
            <button class="btn btn-next" @click="nextProduct">Next product</button>
          </div>
        </div>
      </div>

    </div>
  </div>
</template>

<style scoped>
/* Scoped overrides or specific styles */
.loader-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100%;
}
.product-content, .unavailable-view {
  display: flex;
  width: 100%;
  gap: 40px;
}
.unavailable-view {
  flex-direction: column;
  align-items: center;
  justify-content: center;
  text-align: center;
}
.rating-circles .circle {
  display: inline-block;
  width: 15px;
  height: 15px;
  border-radius: 50%;
  border: 1px solid var(--color-navy); /* Default border logic needed */
  background: transparent;
  margin-left: 2px;
}

/* Dynamic handling for circles border/bg based on palette effectively requires CSS vars override or explicit rules */
/* We can inherit color from section */
.page-men-section .rating-circles .circle {
  border-color: var(--color-navy);
}
.page-men-section .rating-circles .circle.filled {
  background-color: var(--color-navy);
}

.page-women-section .rating-circles .circle {
  border-color: var(--color-purple);
}
.page-women-section .rating-circles .circle.filled {
  background-color: var(--color-purple);
}
</style>
