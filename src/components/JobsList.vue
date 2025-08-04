<template>
  <div>
    <JobFilterBar :activeFilters="activeFilters" @removeFilter="removeFilter" @removeAll="removeAll" :style="{ visibility: isVisible ? 'visible' : 'hidden' }" />
  
  <div class="jobList" :style="{ marginTop: isVisible ? '2%' : dynamicMarginTop }">
    <div class="jobCard" :class="job.featured ? 'highlight' : '' " v-for="job in filteredJobs" :key="job.id">
        <div class="jobHeader">
            <img :src="`${job.logo}`" alt="Company Logo" class="logo" />
            <div class="jobInfo">
                <p >
                    <span class="companyName">{{ job.company }}</span>
                    <span v-if="job.new" class="new"> NEW!</span> 
                    <span v-if="job.featured" class="featured"> FEATURED</span> 
                </p>

                <h3 class="jobTitle">{{  job.position }}</h3>
                <span class="info">{{ job.postedAt }}</span> <div class="dot"></div>
                <span class="info">{{ job.contract }}</span> <div class="dot"></div>
                <span class="info">{{ job.location }}</span> 
                
            </div>
            <hr class="mobile-hr">
            <div class="skills"> 
                <span class="tag" @click="addToFilters(job.role)">{{ job.role }}</span>
                <span class="tag" @click="addToFilters(job.level)">{{ job.level }}</span>
                <span class="tag" v-for="skill in job.languages" :key="skill" @click="addToFilters(skill)">{{ skill }}</span>
                <span class="tag" v-for="tool in job.tools" :key="tool" @click="addToFilters(tool)">{{ tool }}</span>
            </div>
        </div>
    </div>

  </div>
  </div>
</template>

<script lang="ts">
import { computed, defineComponent, reactive, ref } from 'vue'
import type { PropType } from 'vue'
import type { Job } from '../types/jobType.ts';
import JobFilterBar from './JobFilterBar.vue';

export default defineComponent({
  name: 'App',
  components: {
    JobFilterBar
  }, 
  props: {
    jobs: {
      type: Array as PropType<Job[]>,
      required: true
    },
  },
  setup(props) {

    const activeFilters = reactive<string[]>([]);

    const windowWidth = ref(window.innerWidth);
    const handleResize = () => {
      windowWidth.value = window.innerWidth;
    };

    const dynamicMarginTop = computed(() => {
      if (window.innerWidth < 768) {
        return '80px'; // mobile & tablets
      } else if (window.innerWidth < 1024) {
        return '60px'; // small desktop
      } else {
        return '80px'; // large screens
      }
    });

    function addToFilters(tag: string) {
        if (!activeFilters.includes(tag)) {
            activeFilters.push(tag);
            filterJobsByKeywords(props.jobs, activeFilters)
        }
    }

    function removeFilter(tag: string) {
        activeFilters.splice(activeFilters.indexOf(tag),1);
        filterJobsByKeywords(props.jobs, activeFilters);
    }

    function removeAll() {
        if (activeFilters.length === 0) return;
        activeFilters.splice(0);
        filterJobsByKeywords(props.jobs, activeFilters);
    }

    const filterJobsByKeywords = (jobs: Job[], rawInput: Array<string>): Job[] => {
      
      if (rawInput.length === 0) {
        return jobs;
      }

      const keywords = rawInput
        .toString().toLowerCase()
        .split(',')
        .filter(k => k.trim().length > 0);

        return jobs.filter(job => {
          const searchableText = [
            job.role,
            job.level,
            ...job.languages,
            ...job.tools
          ].join(' ').toLowerCase();

          return keywords.every(kw => searchableText.includes(kw))
        })
      }

    const filteredJobs = computed(() => filterJobsByKeywords(props.jobs, activeFilters));

    const isVisible = computed(() => activeFilters.length > 0);

    return {
      filteredJobs,
      activeFilters,
      isVisible,
      dynamicMarginTop,
      addToFilters,
      removeFilter,
      removeAll,
      handleResize
    }
  
  }

});
</script>
