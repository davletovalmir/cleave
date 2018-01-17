<template>
  <input ref="input" type="text" />
</template>

<script>
  import Cleave from 'cleave.js'
  import 'cleave.js/dist/addons/cleave-phone.ru'

  export default {
    props: {
      value: {
        type: String,
        default: ''
      },
      options: {
        type: Object,
        default: () => ({})
      },
      events: {
        type: Object,
        default: () => ({})
      }
    },

    data () {
      return {
        cleave: null
      }
    },

    methods: {
      emitInput () {
        this.$emit('input', this.$el.value)
        this.$emit('rawValueChanged', this.cleave.getRawValue())
      },
      emitFocus () {
        this.$emit('focus', this.$el)
      }
    },

    mounted () {
      this.$el.value = this.value
      this.cleave = new Cleave(this.$el, this.options)
      Object.keys(this.events).map((key) => {
        this.$refs.input.addEventListener(key, this.events[key])
      })
      if (this.options.maxLength) {
        this.$el.setAttribute('maxlength', this.options.maxLength)
      }

      // in case of cleave.js remove result or properties from cleave instance.
      if (this.cleave.properties && this.cleave.properties.hasOwnProperty('result')) {
        this.$watch('cleave.properties.result', this.emitInput)
      } else {
        this.$el.addEventListener('input', this.emitInput)
      }

      this.$el.addEventListener('focus', this.emitFocus)
    },

    beforeDestroy () {
      this.cleave.destroy()
    },

    watch: {
      options: {
        deep: true,
        handler (val) {
          this.cleave.destroy()
          this.cleave = new Cleave(this.$el, val)
        }
      }
    },
  }
</script>
