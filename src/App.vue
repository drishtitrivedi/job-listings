<template>
<div>
  <header class="header" :style="{ backgroundImage: `url(${headerImage})` }">
  </header>

  <section class="section">
    <JobsList :jobs="jobs"></JobsList>
  </section>
</div>
</template>

<script lang="ts">
import { defineComponent, reactive, ref, computed } from 'vue';
import type { Job } from './types/jobType.ts';
import axios from 'axios';
import JobsList from './components/JobsList.vue';

const headerImage = ref<string>('');

const updateHeaderImage = () => {
  const isMobile = window.innerWidth <= 430;
  headerImage.value = isMobile
    ? './images/bg-header-mobile.svg'
    : './images/bg-header-desktop.svg';
};

const headerStyle = computed(() => ({
  backgroundImage: `url(${headerImage.value})`
}));

export default defineComponent({
  name: 'App',
  components: {
    JobsList
  }, 
  setup() {
    let jobs = reactive<Job[]>([]);

    axios.get('data/data.json').then(response => {
       jobs.push(...response.data);
       console.log(jobs);
      });

    return {
      jobs, headerImage, headerStyle
    };
  }, 
  mounted() {
    updateHeaderImage();
    window.addEventListener('resize', updateHeaderImage);
  }
});


</script>


<style scoped>
header {
  background-color: hsl(180, 29%, 50%);
}
header img {
  display: block;
}


</style>
