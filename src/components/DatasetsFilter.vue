<template>

    <w-select class="my-dropdown"
      :items="itemList"
      :label="prompt"
      v-model="selection"
      :multiple="multiple"
      @update:model-value="selectItem"
    >
    </w-select>

<!--  TODO
closable tag
-->
</template>

<script>
export default {
  name: "DatasetsFilter",

  props: {
    prompt: {
      type: String,
      default: () => "Filter..."
    },
    field: {
      type: String,
      required: true
    },
    multiple: {
      type: Boolean
    }
  },

  data: () => ({
    selection: ""
  }),

  computed: {
    itemList() {
      return this.$store.getters.datasetFieldUnique(this.field).map(e => {
        return {
          label: e
        }
      })
    }
  },

  methods: {
    selectItem() {
      this.$store.commit('setDatasetsFilter', {
        field: this.field,
        values: this.selection
      })
    }
  }
}
</script>

<style scoped>


</style>