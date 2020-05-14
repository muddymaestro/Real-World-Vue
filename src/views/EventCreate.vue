<template>
  <div>
    <h1>Create an Event</h1>
    <form @submit.prevent="createEvent">
      <BaseSelect
        v-model="event.category"
        label="Select a category"
        :options="categories"
        class="field"
        :class="{ error: $v.event.category.$error }"
        @blur="$v.event.category.$touch()" 
      />
      <template v-if="$v.event.category.$error">
        <p v-if="!$v.event.category.requied"
          class="errorMessage">Category is required</p>
      </template>

      <h3>Name & describe your event</h3>
        <BaseInput
          v-model="event.title"
          label="Title"
          type="text"
          placeholder="Add an event title"
          class="field"
          :class="{ error: $v.event.title.$error }"
          @blur="$v.event.title.$touch()"
        />
        <template v-if="$v.event.title.$error">
          <p v-if="!$v.event.title.requied"
          class="errorMessage">Title is required</p>
        </template>

        <BaseInput
          v-model="event.description"
          label="Description"
          type="text"
          placeholder="Add a description"
          class="field"
          :class="{ error: $v.event.description.$error }"
          @blur="$v.event.description.$touch()"
        />
        <template v-if="$v.event.description.$error">
          <p v-if="!$v.event.description.requied"
          class="errorMessage">Description is required</p>
        </template>
          
        <h3>Where is your event?</h3>
        <BaseInput
          v-model="event.location"
          label="Location"
          type="text"
          placeholder="Add a location"
          class="field"
          :class="{ error: $v.event.location.$error }"
          @blur="$v.event.location.$touch()"
        />
        <template v-if="$v.event.location.$error">
          <p v-if="!$v.event.location.requied"
          class="errorMessage">Location is required</p>
        </template>

      <h3>When is your event?</h3>

      <div class="field">
        <label>Date</label>
        <datepicker 
          v-model="event.date" 
          placeholder="Select a date"
          :class="{ error: $v.event.date.$error }"
          @opened="$v.event.date.$touch()" />
      </div>
      <template v-if="$v.event.date.$error">
        <p v-if="!$v.event.date.requied"
        class="errorMessage">Date is required</p>
      </template>

      <BaseSelect
        v-model="event.time"
        label="Select a time"
        :options="times"
        class="field"
        :class="{ error: $v.event.time.$error }"
        @blur="$v.event.time.$touch()" 
      />
      <template v-if="$v.event.time.$error">
        <p v-if="!$v.event.time.requied"
          class="errorMessage">Time is required</p>
      </template>

      <!-- <div class="field">
        <label>Select a time</label>
        <select v-model="event.time">
          <option v-for="time in times" :key="time">{{ time }}</option>
        </select>
      </div> -->

      <BaseButton 
        type="submit" 
        class="button -fill-gradient"
        :disabled="$v.$anyError">Submit</BaseButton>
      <p v-if="$v.$anyError" class="errorMessage">Please fill out all the requied field(s).</p>

      <!-- <input type="submit" class="button -fill-gradient" value="Submit"/> -->
    </form>
  </div>
</template>


<script>
import Datepicker from 'vuejs-datepicker'
import { required, email } from 'vuelidate/lib/validators';

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
      category: {required},
      title: {required},
      description: {required},
      location: {required},
      date: {required},
      time: {required},
    }
  },
  methods: {
    createEvent() {
      this.$v.$touch()
      if(!this.$v.$invalid) {
        this.$store
        .dispatch('event/createEvent', this.event)
        .then(() => {
          this.$router.push({
            name: 'event-show',
            params: { id: this.event.id }
          })
          this.event = this.createFreshEventObject()
        })
        .catch(() => {}) 
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

.error, .errorMessage {
  color: red;
}
</style>
