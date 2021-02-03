<template>
  <section>
    <div v-for="(trip, index) in trips" :key="index">
      <img :src="trip.titleImageUrl" />
      <h3 v-text="trip.title" />
      <span v-text="trip.location" />
      <p v-text="trip.description" />
    </div>
    <div v-if="authorized">
      <h3>Add new trip</h3>
      <div>
        <input v-model="title" placeholder="Title" />
      </div>
      <div>
        <input v-model="image" placeholder="Image address" />
      </div>
      <div>
        <input v-model="location" placeholder="Location" />
      </div>
      <div>
        <textarea v-model="description" placeholder="Description" />
      </div>
      <button @click="createTrip">Add new trip</button>
    </div>
  </section>
</template>
<script>
export default {
  data: () => ({
    trips: [],
    title: null,
    location: null,
    image: null,
    description: null,
    authorized: false
  }),

  mounted() {
    this.getTrips()

    if (sessionStorage.getItem('token')) {
      this.authorized = true
    }
  },

  methods: {
    async getTrips() {
      const query = `
        query {
          offers{
            title
            location,
            description,
            titleImageUrl,
          }
        }
        `
      const trips = await this.axios.post('http://localhost:9000/query', { query })

      this.trips = trips.data.data.offers
    },

    async createTrip() {
      const query = `
        mutation {
          createOffer(input: {title: "${this.title}", location: "${this.location}", description: "${this.description}", titleImageUrl: "${this.image}"}){
            id,
            title,
            location,
            description,
            titleImageUrl,
            user{
              name
            }
          }
        }
        `
      const trips = await this.axios.post('http://localhost:9000/query', { query }, { headers: { 'Authorization': 'Bearer ' + sessionStorage.getItem('token')} })
      console.log(trips)
    }
  }
}
</script>
