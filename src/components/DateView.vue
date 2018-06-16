<template>
  <div class="dateV-wrapper">
    <DateVCalend></DateVCalend>
    <DateVDetails></DateVDetails>
  </div>
</template>
<script>
import DateVCalend from '@/components/DateVCalend'
import DateVDetails from '@/components/DateVDetails'
export default {
  name: 'dateView',
  data () {
    return {
      // today: new Date()
    }
  },
  components: {
    DateVCalend,
    DateVDetails
  }
}
</script>
<style>
.dateV-wrapper {
  width: 100%;
  height: 100%;
  background: #ffffff;
  display: flex;
  flex-direction: column;
  user-select: none;
}
</style>
