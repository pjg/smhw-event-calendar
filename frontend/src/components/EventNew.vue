<template>
  <fieldset>
    <p v-if="error" class="error">{{ error }}</p>

    <h1>Add new Event</h1>

    <p>
      <input
        v-model.trim="title"
        v-on:keyup="validate"
        type="text"
        name="title"
        maxlength="25"
        placeholder="Title..."
        autofocus />
    </p>
    <p>
      <input
        v-model.trim="location"
        type="text"
        name="location"
        maxlength="25"
        placeholder="Location..." />
    </p>
    <p>
      <input
        v-model.trim="description"
        type="text"
        name="description"
        maxlength="35"
        placeholder="Description..." />
    </p>
    <p>
      Start date:
      <input
        v-model="start_date"
        v-on:change="validate"
        type="date"
        name="start_date"
        :min="min"
        :max="max" />
    </p>
    <p>
      End date:
      <input
        v-model="end_date"
        v-on:change="validate"
        type="date"
        name="end_date"
        :min="min"
        :max="max" />
    </p>

    <p class="actions">
      <button
        v-bind:disabled="!valid"
        @click="createEvent"
        class="create-event">
        Create Event
      </button>
    </p>
  </fieldset>
</template>

<script>
import moment from 'moment'
import { dateFormat } from '@/constants'

const today = moment().format(dateFormat)

export default {
  name: 'EventNew',

  props: {
    min: {
      type: String,
      required: true
    },
    max: {
      type: String,
      required: true
    }
  },

  data () {
    return {
      error: null,
      valid: false,
      title: '',
      location: '',
      description: '',
      start_date: today,
      end_date: today
    }
  },

  methods: {
    createEvent () {
      this.$http.post('/api/events', {
        event: {
          title: this.title,
          location: this.location,
          description: this.description,
          start_date: this.start_date,
          end_date: this.end_date
        }
      }).then(response => {
        this.$emit('add-event', response.data)
        this.error = null
      }, response => {
        this.error = 'There was an error when adding the event'
      })
    },

    validate () {
      this.valid =
        this.title.length > 0 &&
        this.start_date.length > 0 &&
        this.end_date.length > 0
    }
  }
}
</script>

<style>
fieldset {
  border: 1px solid #ccc;
  margin: 0.5rem 0 0;
  padding: 0.5rem 1.5rem;
  width: 20rem;
}

fieldset input[type=text] {
  font-size: 0.9rem;
  padding: 0.5rem;
  width: 80%;
}

fieldset input[type=date] {
  font-size: 1rem;
  padding: 0.5rem;
}
</style>
