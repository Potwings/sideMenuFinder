<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Header -->
    <header class="bg-white border-b border-gray-200 shadow-sm">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <div class="flex justify-between items-center py-4">
          <!-- Logo -->
          <div class="text-xl font-bold text-gray-900">
            MenuFinder
          </div>
          
          <!-- Navigation -->
          <nav class="flex items-center space-x-8">
            <a href="#" class="text-gray-700 hover:text-gray-900 font-medium text-sm">홈</a>
            <a href="#" class="text-gray-700 hover:text-gray-900 font-medium text-sm">주문내역</a>
            <a href="#" class="text-gray-700 hover:text-gray-900 font-medium text-sm">즐겨찾기</a>
            <a href="#" class="text-gray-700 hover:text-gray-900 font-medium text-sm">프로필</a>
            <div class="w-8 h-8 bg-gradient-to-r from-purple-400 to-pink-400 rounded-full"></div>
          </nav>
        </div>
      </div>
    </header>

    <!-- Main Content -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
      <!-- Hero Section -->
      <section class="text-center mb-12">
        <h1 class="text-4xl font-bold text-gray-900 mb-4">
          완벽한 사이드 메뉴를 찾아보세요
        </h1>
        <p class="text-xl text-gray-600 mb-8">
          다양한 음식점과 사이드 메뉴를 탐색하여 당신의 입맛을 만족시켜보세요.
        </p>
      </section>

      <!-- Search Section -->
      <section class="mb-12">
        <form @submit.prevent="searchRestaurants" class="max-w-2xl mx-auto">
          <div class="space-y-4">
            <div class="relative">
              <input
                v-model="searchParams.store"
                type="text"
                placeholder="음식점이나 카테고리를 검색하세요"
                class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none text-lg"
              />
            </div>
            
            <div class="relative">
              <input
                v-model="searchParams.menu"
                type="text"
                placeholder="원하는 메뉴를 검색하세요"
                class="w-full px-4 py-3 rounded-lg border border-gray-300 focus:ring-2 focus:ring-blue-500 focus:border-transparent outline-none text-lg"
              />
            </div>
            
            <div class="text-center">
              <button
                type="submit"
                :disabled="loading"
                class="bg-blue-600 hover:bg-blue-700 disabled:bg-gray-400 text-white font-semibold py-3 px-8 rounded-lg text-lg transition-colors duration-200"
              >
                {{ loading ? '검색 중...' : '검색' }}
              </button>
            </div>
          </div>
        </form>
      </section>

      <!-- Loading State -->
      <div v-if="loading" class="text-center py-12">
        <div class="inline-block animate-spin rounded-full h-8 w-8 border-b-2 border-blue-600"></div>
        <p class="mt-4 text-gray-600">검색 중입니다...</p>
      </div>

      <!-- Error State -->
      <div v-if="error" class="bg-red-50 border border-red-200 rounded-lg p-4 mb-8">
        <div class="flex">
          <div class="flex-shrink-0">
            <svg class="h-5 w-5 text-red-400" viewBox="0 0 20 20" fill="currentColor">
              <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
            </svg>
          </div>
          <div class="ml-3">
            <p class="text-sm text-red-800">{{ error }}</p>
          </div>
        </div>
      </div>

      <!-- Search Results -->
      <section v-if="searchResults.length && !loading" class="mb-12">
        <h2 class="text-2xl font-bold text-gray-900 mb-6">
          검색 결과 ({{ searchResults.length }}개 음식점)
        </h2>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <div
            v-for="store in searchResults"
            :key="store.storeName"
            @click="openStoreURL(store.storeURL)"
            class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-lg transition-shadow duration-300 cursor-pointer"
            :class="{ 'hover:shadow-xl': store.storeURL }"
          >
            <!-- Store Image/Icon -->
            <div class="h-48 bg-gradient-to-br from-blue-400 to-purple-600 flex items-center justify-center">
              <span class="text-white font-bold text-xl">{{ store.storeName.charAt(0) }}</span>
            </div>
            
            <!-- Store Info -->
            <div class="p-6">
              <h3 class="text-xl font-semibold text-gray-900 mb-3">
                {{ store.storeName }}
              </h3>
              
              <!-- Menu List -->
              <div class="space-y-2">
                <h4 class="text-sm font-medium text-gray-700 mb-2">메뉴 목록:</h4>
                <div class="flex flex-wrap gap-2">
                  <span
                    v-for="menu in store.menuList"
                    :key="menu"
                    class="inline-flex items-center px-3 py-1 rounded-full text-sm font-medium bg-blue-100 text-blue-800"
                  >
                    {{ menu }}
                  </span>
                </div>
              </div>
            </div>
          </div>
        </div>
      </section>

      <!-- No Results -->
      <div v-if="hasSearched && !searchResults.length && !loading" class="text-center py-12">
        <svg class="mx-auto h-12 w-12 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9.172 16.172a4 4 0 015.656 0M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
        </svg>
        <h3 class="mt-4 text-lg font-medium text-gray-900">검색 결과가 없습니다</h3>
        <p class="mt-2 text-gray-600">검색 조건에 맞는 음식점을 찾을 수 없습니다.</p>
      </div>

      <!-- Popular Restaurants Section (when no search) -->
      <section v-if="!searchResults.length && !hasSearched" class="mb-12">
        <h2 class="text-2xl font-bold text-gray-900 mb-6">사이드 메뉴로 인기 있는 음식점</h2>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <div
            v-for="restaurant in popularRestaurants"
            :key="restaurant.id"
            class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-lg transition-shadow duration-300"
          >
            <div class="h-48 bg-gradient-to-br from-green-400 to-blue-500 flex items-center justify-center">
              <span class="text-white font-bold text-xl">{{ restaurant.name.charAt(0) }}</span>
            </div>
            <div class="p-6">
              <h3 class="text-xl font-semibold text-gray-900 mb-2">{{ restaurant.name }}</h3>
              <p class="text-gray-600">{{ restaurant.category }}</p>
            </div>
          </div>
        </div>

        <h2 class="text-2xl font-bold text-gray-900 mb-6 mt-12">근처 추천 음식점</h2>
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          <div
            v-for="restaurant in nearbyRestaurants"
            :key="restaurant.id"
            class="bg-white rounded-lg shadow-md overflow-hidden hover:shadow-lg transition-shadow duration-300"
          >
            <div class="h-48 bg-gradient-to-br from-purple-400 to-pink-500 flex items-center justify-center">
              <span class="text-white font-bold text-xl">{{ restaurant.name.charAt(0) }}</span>
            </div>
            <div class="p-6">
              <h3 class="text-xl font-semibold text-gray-900 mb-2">{{ restaurant.name }}</h3>
              <p class="text-gray-600">평점: {{ restaurant.rating }}</p>
            </div>
          </div>
        </div>
      </section>
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
        
        console.log('백엔드 응답:', response.data)
        
        // 백엔드에서 StoreInfo 객체들의 배열을 반환한다고 가정
        // StoreInfo: { storeName: String, menuList: List<String>, storeURL: String }
        this.searchResults = response.data || []
      } catch (error) {
        console.error('검색 오류:', error)
        this.error = '검색 중 오류가 발생했습니다. 잠시 후 다시 시도해주세요.'
        this.searchResults = []
      } finally {
        this.loading = false
      }
    },
    
    openStoreURL(storeURL) {
      if (storeURL) {
        window.open(storeURL, '_blank')
      }
    }
  }
}
</script>

<style scoped>
/* Component-specific styles can be added here if needed */
</style> 