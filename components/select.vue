<template>
  <div class="custom-select">
    <label v-if="label" :for="id">{{ label }}</label>
    <select :id="id" v-model="selected" @change="emitSelection">
      <option v-for="option in options" :key="option.value" :value="option.value">
        {{ option.text }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: 'CustomSelect',
  props: {
    options: {
      type: Array,
      required: true
    },
    value: {
      type: [String, Number],
      default: ''
    },
    label: {
      type: String,
      default: ''
    },
    id: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      selected: ''
    };
  },
  methods: {
    emitSelection() {
      let option = this.options.find(option => option.value === this.selected);
      this.$emit('selectedText', option.text)
      this.$emit('selectedValue', option.value)
    }
  }
};
</script>

<style scoped>
</style>