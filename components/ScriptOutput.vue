<template>

  <v-dialog v-model="show">

    <v-card>
      <v-card-text>
        <v-btn @click.stop="show=false"
          color="warning"
          absolute right
          fab small
          >
          <v-icon>close</v-icon>
        </v-btn>
        <p class="pa-4">{{outputString}}</p>
        <v-btn color="primary" @click.stop="copyScript">Copy Script</v-btn>
      </v-card-text>
    </v-card>
  </v-dialog>
</template>

<script>
  import copy from 'copy-text-to-clipboard'

  export default {
    props: {
      'outputString': String,
      'showScript': Boolean
    },
    methods: {
      copyScript () {
        copy(this.outputString);
      }
    },
    computed: {
      show: {
        get () {
          return this.showScript
        },
        set (value) {
          if (!value) {
            this.$emit('close')
          }
        }
      }
    }
  }
</script>

<style scoped>
  .card__text {
    text-align: center;
  }
  p {
    text-align: left;
    white-space: pre;
  }
</style>
