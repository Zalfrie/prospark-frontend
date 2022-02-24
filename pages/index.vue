<template>
  <div class="bg-gray-100 w-full min-h-screen p-4">
    <div class="flex flex-col space-y-2 container mx-auto ">
      <div class="bg-white p-2 rounded-md flex flex-col md:flex-row md:space-y-0 space-y-2 justify-between items-center">
        <p class="text-xl font-semibold">
          Product List
        </p>
      </div>
      <div v-if="data.length > 0">
        <div v-for="(item, key) in data" :key="key" class="bg-white p-4">
          <p class="text-xl">
            {{ item.name }}
          </p>
          <p class="text-sm">
            {{ item.description }}
          </p>
          <p class="text-sm">
            {{ item.qty }}
          </p>
          <p class="text-base font-semibold">
            {{ item.price }}
          </p>
        </div>
      </div>
      <div v-else>
        <div class="flex flex-col rounded-md bg-white items-center space-y-2 justify-center p-4">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 7v10c0 2.21 3.582 4 8 4s8-1.79 8-4V7M4 7c0 2.21 3.582 4 8 4s8-1.79 8-4M4 7c0-2.21 3.582-4 8-4s8 1.79 8 4m0 5c0 2.21-3.582 4-8 4s-8-1.79-8-4" />
          </svg>
          No Data Available !
        </div>
      </div>
      <div v-if="metadata.next_page_url != null && isLoading === false" class="flex items-center justify-center py-4">
        <button class="ml-2 text-white hover:bg-blue-700 bg-blue-600 rounded-md py-2 px-4" @click="getNextPage">
          Load More
        </button>
      </div>

      <div v-if="isLoading" class="flex justify-center items-center">
        <div class="animate-spin h-5 w-5 mr-3 items-center flex justify-center">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 9v3m0 0v3m0-3h3m-3 0H9m12 0a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
        </div> Loading
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ProductPage',
  data () {
    return {
      data: [],
      metadata: {
        next_page_url: null
      },
      query: {
        q: ''
      },
      isLoading: false
    }
  },
  beforeMount () {
    this.getData()
  },
  mounted () {
    this.getNextPage()
  },
  methods: {
    getData () {
      const self = this
      this.isLoading = true
      this.$axios.$get('http://prospark-backend.local/api/v1/product', {
        params: {
          q: self.query.q
        }
      }).then((response) => {
        // self.data = Array.prototype.push.apply(self.data, response.data);
        self.data = response.data
        self.metadata = response
        // eslint-disable-next-line no-console
        console.log(JSON.stringify(self.data))
        self.isLoading = false
      }).catch((error) => {
        this.error = true
        // eslint-disable-next-line no-console
        console.log(error)
      }).finally(() => {
        this.isLoading = false
      })
    },
    getNextPage () {
      const self = this
      this.isLoading = true
      this.$axios.$get(self.metadata.next_page_url, {
        params: {
          q: self.query.q
        }
      }).then((response) => {
        self.data.push(...response.data)
        self.metadata = response
        // eslint-disable-next-line no-console
        console.log(JSON.stringify(self.data))
        // eslint-disable-next-line no-console
        console.log(self.data.length)
      }).catch((error) => {
        this.error = true
        // eslint-disable-next-line no-console
        console.log(error)
      }).finally(() => {
        this.isLoading = false
      })
    }
  }
}
</script>
