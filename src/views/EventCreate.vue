<template>
  <div>
    <h1>Create an Event</h1>
    <form @submit.prevent="createEvent">
      <BaseSelect v-model="event.category" 
                  :options="categories" 
                  label="Select a category"
                  :class="{ error: $v.event.category.$error }"
                  @blur="$v.event.category.$touch()"/>

      <template v-if="$v.event.category.$error">
        <p v-if="!$v.event.category.required" class="errorMessage">Category is required</p>
      </template>

      <h3>Name & describe your event</h3>
      <BaseInput v-model="event.title" 
                 label="Title"
                 type="text"
                 placeholder="Title"
                 class="field"
                :class="{ error: $v.event.title.$error }"
                 @blur="$v.event.title.$touch()"/>

      <template v-if="$v.event.title.$error">
        <p v-if="!$v.event.title.required" class="errorMessage">Title is required</p>
      </template>


      <BaseInput v-model="event.description"
                 label="Description"
                 type="text"
                 placeholder="Description"
                 :class="{ error: $v.event.description.$error }"
                 @blur="$v.event.description.$touch()"
                 class="field"/>

      <template v-if="$v.event.description.$error">
        <p v-if="!$v.event.description.required" class="errorMessage">Description is required</p>
      </template>

      <BaseInput v-model="event.location"
                 label="Location"
                 type="text"
                 placeholder="Add a location"
                 :class="{ error: $v.event.location.$error }"
                 @blur="$v.event.location.$touch()"
                 class="field"/>

      <template v-if="$v.event.location.$error">
        <p v-if="!$v.event.location.required" class="errorMessage">Location is required</p>
      </template>

      <h3>When is your event?</h3>

      <div class="field">
        <label>Date</label>
        <datepicker v-model="event.date"
                   :input-class="{ error: $v.event.date.$error }"
                   @opened="$v.event.date.$touch()"
                   placeholder="Select a date"/>
      </div>

      <template v-if="$v.event.date.$error">
        <p v-if="!$v.event.date.required" class="errorMessage">Date is required</p>
      </template>

      <BaseSelect v-model="event.time"
                  :options="times" 
                  label="Select a time"
                  @blur="$v.event.time.$touch()"
                  :class="{ error: $v.event.time.$error }"/>

      <template v-if="$v.event.time.$error">
        <p v-if="!$v.event.time.required" class="errorMessage">Time is required</p>
      </template>
      
      <BaseButton type="submit" :disabeled="$v.$anyError">Submit</BaseButton>
    </form>
  </div>
</template>


<script>
import Datepicker from 'vuejs-datepicker'
import NProgress from 'nprogress'
import { required } from 'vuelidate/lib/validators'

export default {

  components: {
    Datepicker
  },

  data() {
    const times = []
    for (let i = 1; i <= 24; i++) {
      times.push(i + ':00')
    }
    return {
      times,
      categories: this.$store.state.categories,
      event: this.createFreshEventObject()
    }
  },

validations: {
    event: {
      category:    { required },
      title:       { required },
      description: { required },
      location:    { required },
      date:        { required },
      time:        { required }
    }  
  },

  methods: {
    createEvent() {
      this.$v.$touch()
      if (!this.$v.invalid) {
        NProgress.start()
        this.$store
          .dispatch('event/createEvent', this.event)
          .then(() => {
            this.$router.push({
              name: 'event-show',
              params: { id: this.event.id }
            })
            this.event = this.createFreshEventObject()
          })
          .catch(() => {
            NProgress.done()
          })
      }
    },

    createFreshEventObject() {
      const user = this.$store.state.user.user
      const id = Math.floor(Math.random() * 10000000)

      return {
        id: id,
        user: user,
        category: '',
        organizer: user,
        title: '',
        description: '',
        location: '',
        date: '',
        time: '',
        attendees: []
      }
    }
  }
}
</script>

<style scoped>
.field {
  margin-bottom: 24px;
}
</style>
