<template>
  <div class="col-md-6 col-lg-4">
    <div class="card mb-4">
      <img
        class="card-img-top"
        :src="restaurant.image | emptyImage"
        alt="Card image cap"
        width="286px"
        height="180px"
      >
      <div class="card-body">
        <p class="card-text title-wrap">
          <router-link :to="{ name: 'restaurant', params: {id: restaurant.id} }">{{ restaurant.name }}</router-link>
        </p>
        <span class="badge badge-secondary">{{ restaurant.Category? restaurant.Category.name : '未分類' }}</span>
        <p class="card-text text-truncate">
          {{ restaurant.description }}
        </p>
      </div>
      <div class="card-footer">
        <button
          type="button"
          class="btn btn-danger btn-border favorite mr-2"
          v-if="restaurant.isFavorited"
          @click.prevent.stop="deleteFavorite(restaurant.id)"
        >
          移除最愛
        </button>
        <button
          type="button"
          class="btn btn-primary btn-border favorite mr-2"
          v-else
          @click.prevent.stop="addFavorite(restaurant.id)"
        >
          加到最愛
        </button>
        <button
          type="button"
          class="btn btn-danger like mr-2"
          v-if="restaurant.isLiked"
          @click.prevent.stop="deleteLike(restaurant.id)"
        >
          Unlike
        </button>
        <button
          type="button"
          class="btn btn-primary like mr-2"
          v-else
          @click.prevent.stop="addLike(restaurant.id)"
        >
          Like
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { emptyImageFilter } from './../utils/mixins'
import usersAPI from './../apis/users'
import { Toast } from './../utils/helpers'

export default {
  props: {
    initialRestaurant: {
      type: Object,
      required: true
    }
  },
  data() {
    return {
      restaurant: this.initialRestaurant
    }
  },
  mixins: [emptyImageFilter],
  methods: {
    async addFavorite(restaurantId) {
      try {
        const { data } = await usersAPI.addFavorite({restaurantId})

        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurant = {
          ...this.restaurant,
          isFavorited: true
        }
      } catch(e) {
        Toast.fire({
          icon: 'error',
          title: '無法將餐廳加入最愛，請稍後再試'
        })
        console.log(e)
      }
    },
    async deleteFavorite(restaurantId) {
      try {
        const { data } = await usersAPI.deleteFavorite({restaurantId})

        if (data.status !== 'success') {
          throw new Error(data.message)
        }
        this.restaurant = {
          ...this.restaurant,
          isFavorited: false
        }
      } catch(e) {
        Toast.fire({
          icon: 'error',
          title: '無法將餐廳移除最愛，請稍後再試'
        })
        console.log(e)
      }
    },
    async addLike(restaurantId) {
      try {
        const { data } = await usersAPI.addLike({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.status)
        }
        this.restaurant = {
        ...this.restaurant,
        isLiked: true
        }
      } catch(e) {
        Toast.fire({
          icon: 'error',
          title: '無法加入喜愛餐廳，請稍後再試'
        })
        console.log(e)
      }

    },
    async deleteLike(restaurantId) {
      try {
        const { data } = await usersAPI.deleteLike({restaurantId})
        if (data.status !== 'success') {
          throw new Error(data.status)
        }
        this.restaurant = {
        ...this.restaurant,
        isLiked: false
        }
      } catch(e) {
        Toast.fire({
          icon: 'error',
          title: '無法移除喜愛餐廳，請稍後再試'
        })
        console.log(e)
      }
    }
  }
}
</script>