<template>
  <div class="container py-5">
    <div>
      <h1>{{ restaurant.name }}</h1>
      <span class="badge badge-secondary mt-1 mb-3">
        {{ restaurant.categoryName }}
      </span>
    </div>

    <hr>

    <ul class="list-unstyled">
      <li>評論數： {{ restaurant.commentsLength }}</li>
      <li>瀏覽次數： {{ restaurant.viewCounts }}</li>
    </ul>

    <button
      type="button"
      class="btn btn-link"
      @click="$router.back()"
    >
      回上一頁
    </button>
  </div>
</template>

<script>
import restaurantsAPI from './../apis/restaurants'
import { Toast } from './../utils/helpers'


export default {
  data() {
    return {
      restaurant: {
        id: undefined,
        name: '',
        categoryName: '',
        commentsLength: 0,
        viewCounts: 0
      }
    }
  },
  methods: {
    async fetchRestaurant(restaurantId) {
      try {
        const response = await restaurantsAPI.getRestaurant({ restaurantId })
        if (response.statusText !== 'OK') {
          throw new Error(response.statusText)
        }
        const {id, name, Category, Comments, viewCounts} = response.data.restaurant
        this.restaurant = {
          id,
          name,
          categoryName: Category? Category.name : '未分類',
          commentsLength: Comments.length,
          viewCounts
        }
      } catch(e) {
        Toast.fire({
          icon: 'error',
          title: '無法取得餐廳資訊，請稍後再試'
        })
        console.log(e)
      }
      
    }
  },
  created() {
    const { id } = this.$route.params
    this.fetchRestaurant(id)
  }
}
</script>