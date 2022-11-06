<template>
    <w-flex wrap>
      <div class="xs6 px-1">

        <w-card class="my2" title="Data selection:">
          <DatasetsFilter prompt="Organism..." field="organism" class="justify-start"/>
          <DatasetsFilter prompt="Organ..." field="organ" multiple/>
          <DatasetsFilter prompt="Cell types..." field="cell_types" multiple/>
        </w-card>

        <w-card class="my2" title="Sampling:">
          <w-select class="my-dropdown"
            :items="sampling_items"
            label="Sampling scheme..."
            v-model="sampling_scheme"
          >
          </w-select>
          <label class="primary w-select my-dropdown">
            Number of cells per sample...
          </label>
          <w-flex align-center style="margin-top: 50px">
            <w-button
              @click="decreaseSamplingSlider(100)"
              icon="wi-minus"
              sm>
            </w-button>
            <w-slider
              class="mx3 grow"
              v-model="SamplingSlider"
              :min="100"
              :max="10000"
              thumb-label
              >
            </w-slider>
            <w-button
              @click="increaseSamplingSlider(10000)"
              icon="wi-plus"
              sm>
            </w-button>
          </w-flex>
        </w-card>

        <w-card class="my2" title="Probeset selection:">

<!--          <div style="text-align: left">-->
<!--            Number of genes:<code class="ml1">{{ sliderValue }} </code>-->
<!--          </div>-->
          <label class="primary w-select">
            Number of genes...
          </label>
          <w-flex align-center style="margin-top: 50px">
            <w-button
              @click="decreaseGeneSlider(0)"
              icon="wi-minus"
              sm>
            </w-button>
            <w-slider
              class="mx3 grow"
              v-model="GeneSlider"
              :min="0"
              :max="200"
              thumb-label
              >
            </w-slider>
            <w-button
              @click="increaseGeneSlider(200)"
              icon="wi-plus"
              sm>
            </w-button>
          </w-flex>

          <w-select class="my-dropdown"
            :items="selection_method_items"
            label="Selection method..."
            v-model="selection_method"
          >
          </w-select>
        </w-card>



      </div>

      <div class="xs6 px-1">
        <w-card class="my2">
          <div class="py-2 my-1 mx-auto overflow-auto flex-grow" style="max-height: 304px">
            <DatasetsCardList :items="datasets" />
          </div>
        </w-card>
        <w-card class="my1 overflow-x-auto" title="Config preview:" style="max-height: 551px; align-items: flex-start">
          <pre class="primary w-select">{{ config }}</pre>


        </w-card>
      </div>

    </w-flex>
</template>

<script>
import {ref, computed} from "vue";
import store from "../store/index.js";

import DatasetsFilter from "./DatasetsFilter.vue";
import DatasetsCardList from "./DatasetsCardList.vue";
import {list} from "postcss";



export default {
  name: "MainPage",

  components: {
    DatasetsFilter,
    DatasetsCardList
  },

  setup() {

    let GeneSlider = ref(50)
    let SamplingSlider = ref(100)
    let sampling_scheme=ref( "None")
    let selection_method=ref( "Spapros")


    let increaseGeneSlider = function(max) {
      GeneSlider.value = Math.min(GeneSlider.value + 1, max)
    }

    let decreaseGeneSlider = function(min, slider) {
      GeneSlider = Math.max(GeneSlider.value - 1, min)
    }

    let increaseSamplingSlider = function(max) {
      SamplingSlider.value = Math.min(SamplingSlider.value + 1, max)
    }

    let decreaseSamplingSlider = function(min) {
      SamplingSlider.value = Math.max(SamplingSlider.value - 1, min)
    }

    let selection_method_items = [
      { label: 'Spapros' },
      { label: 'DE' },
      { label: 'DE_batch_aware' }
    ]

    let sampling_items = [
      { label: "Dataset balanced" },
      { label: "Cell type balanced" }
    ]

    let datasets = computed( () =>{
      return store.getters.displayDatasets
    })

    let config = computed( () => {
      let ids = []
      let ds = store.getters.displayDatasets
      for (let d in ds) {
        ids.push(ds[d]["id"])
      }
      return JSON.stringify({
        "sfaira": {"ids": ids},
        "sampling": {
          "sampling scheme": sampling_scheme.value,
          "sampling size": parseInt(SamplingSlider.value.toString())
        },
        "spapros": {
          "n": GeneSlider.value,
          "selection method": selection_method.value
        }
      }, null, 2)
    })

    return {
      GeneSlider,
      increaseGeneSlider,
      decreaseGeneSlider,
      SamplingSlider,
      increaseSamplingSlider,
      decreaseSamplingSlider,
      sampling_scheme,
      selection_method_items,
      selection_method,
      sampling_items,
      config,
      datasets
    }
  },
}
</script>

<style scoped>

</style>