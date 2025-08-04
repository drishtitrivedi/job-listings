<template>

  <div class="filterBar">
    <span
      v-for="tag in activeFilters"
      :key="tag"
      class="filterTag"
    >
      {{ tag }} <span class="closeBtn" @click="handleClick(tag)"><img src="/images/icon-remove.svg" alt="Logo" /></span>
    </span> 

    <span class="clearBtn" @click="removeFilters()">Clear</span>
  </div>


</template>



<script lang="ts">
import {  defineComponent } from 'vue';
import type { PropType } from 'vue';

export default defineComponent({
  name: 'JobFilterBar',
  props: {
      activeFilters: {
          type: Array as PropType<string[]>,
          required: true
      }
  },
  emits: ['removeFilter','removeAll'],
  setup( props, { emit }) {
 
       function handleClick(value: string) {
            emit('removeFilter',value);
        }

        function removeFilters() {
            emit('removeAll');
        }


    return {
        handleClick,
        removeFilters
    }
},  

});
</script>





