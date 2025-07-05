<template>
  <div class="app">
    <!-- Header -->
    <header class="header">
      <div class="nav-container">
        <!-- Logo -->
        <div class="logo">
          MenuFinder
        </div>
        
        <!-- Navigation -->
        <nav class="nav-menu">
          <a href="#" class="nav-item">Home</a>
          <a href="#" class="nav-item">Orders</a>
          <a href="#" class="nav-item">Favorites</a>
          <a href="#" class="nav-item">Profile</a>
          <div class="profile-avatar"></div>
        </nav>
      </div>
    </header>

    <!-- Main Content -->
    <main class="main-content">
      <!-- Hero Section -->
      <section class="hero-section">
        <h1 class="hero-title">완벽한 사이드 메뉴를 찾아보세요</h1>
        <p class="hero-subtitle">다양한 음식점과 사이드 메뉴를 탐색하여 당신의 입맛을 만족시켜보세요.</p>
      </section>

      <!-- Search Section -->
      <section class="search-section">
        <form @submit.prevent="searchRestaurants" class="search-form">
          <div class="search-input-group">
            <input
              v-model="searchParams.store"
              type="text"
              placeholder="음식점이나 카테고리를 검색하세요"
              class="search-input"
            />
          </div>
          
          <div class="search-input-group">
            <input
              v-model="searchParams.menu"
              type="text"
              placeholder="원하는 메뉴를 검색하세요"
              class="search-input"
            />
          </div>
          
          <div class="search-button-container">
            <button type="submit" class="search-button" :disabled="loading">
              {{ loading ? '검색 중...' : '검색' }}
            </button>
          </div>
        </form>
      </section>

      <!-- Popular Restaurants Section -->
      <section v-if="!searchResults.length && !hasSearched">
        <h2 class="section-title">사이드 메뉴로 인기 있는 음식점</h2>
        <div class="card-grid">
          <div class="restaurant-card" v-for="restaurant in popularRestaurants" :key="restaurant.id">
            <div class="card-image">{{ restaurant.name }}</div>
            <div class="card-content">
              <h3 class="card-title">{{ restaurant.name }}</h3>
              <p class="card-subtitle">{{ restaurant.category }}</p>
            </div>
          </div>
        </div>

        <h2 class="section-title">근처 추천 음식점</h2>
        <div class="results-grid">
          <div class="result-card" v-for="restaurant in nearbyRestaurants" :key="restaurant.id">
            <div class="result-image">{{ restaurant.name }}</div>
            <div class="card-content">
              <h3 class="card-title">{{ restaurant.name }}</h3>
              <p class="card-subtitle">{{ restaurant.rating }}</p>
            </div>
          </div>
        </div>
      </section>

      <!-- Loading State -->
      <div v-if="loading" class="loading">
        검색 중입니다...
      </div>

      <!-- Error State -->
      <div v-if="error" class="error">
        {{ error }}
      </div>

      <!-- Search Results -->
      <section v-if="searchResults.length && !loading" class="results-section">
        <h2 class="section-title">검색 결과 ({{ searchResults.length }}개 음식점)</h2>
        <div class="results-grid">
          <div class="result-card" v-for="restaurant in searchResults" :key="restaurant.id">
            <div class="result-image">{{ restaurant.name }}</div>
            <div class="card-content">
              <h3 class="card-title">{{ restaurant.name }}</h3>
              <p class="card-subtitle">{{ restaurant.menu }} 메뉴 보유</p>
              <p class="card-subtitle">{{ restaurant.category }}</p>
              <p class="card-subtitle" v-if="restaurant.rating">평점: {{ restaurant.rating }}</p>
            </div>
          </div>
        </div>
      </section>

      <!-- No Results -->
      <div v-if="hasSearched && !searchResults.length && !loading" class="no-results">
        검색 조건에 맞는 음식점을 찾을 수 없습니다.
      </div>
    </main>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
    return {
      searchParams: {
        store: '',
        menu: ''
      },
      searchResults: [],
      loading: false,
      error: null,
      hasSearched: false,
      popularRestaurants: [
        {
          id: 1,
          name: "김씨네 부엌",
          category: "한식 사이드 메뉴"
        },
        {
          id: 2,
          name: "스시 사이드 바이츠",
          category: "일식 사이드 메뉴"
        },
        {
          id: 3,
          name: "딤섬 딜라이트",
          category: "중식 사이드 메뉴"
        }
      ],
      nearbyRestaurants: [
        {
          id: 4,
          name: "더 코너 카페",
          rating: "4.5점"
        },
        {
          id: 5,
          name: "스파이스 루트",
          rating: "4.2점"
        },
        {
          id: 6,
          name: "오션스 캐치",
          rating: "4.8점"
        }
      ]
    }
  },
  methods: {
    async searchRestaurants() {
      if (!this.searchParams.store && !this.searchParams.menu) {
        this.error = '음식점명이나 메뉴명 중 하나는 입력해주세요.'
        return
      }

      this.loading = true
      this.error = null
      this.hasSearched = true

      try {
        const response = await axios.get('http://localhost:8080/search/store', {
          params: {
            storeFindKeyword: this.searchParams.store,
            menu: this.searchParams.menu
          }
        })
        
        console.log(response.data)
        this.searchResults = response.data || []
      } catch (error) {
        console.error('검색 오류:', error)
        this.error = '검색 중 오류가 발생했습니다. 잠시 후 다시 시도해주세요.'
        this.searchResults = []
      } finally {
        this.loading = false
      }
    }
  }
}
</script>

<style scoped>
/* Component-specific styles can be added here if needed */
</style> 