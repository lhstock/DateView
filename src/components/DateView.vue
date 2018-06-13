<template>
  <div class="dateV-wrapper">
    <DateVCalend></DateVCalend>
    <DateVDetails></DateVDetails>
  </div>
</template>
<script>
import DateVCalend from "@/components/DateVCalend";
import DateVDetails from "@/components/DateVDetails";
export default {
  name: "dateView",
  data() {
    return {
      // today: new Date()
    };
  },
  components: {
    DateVCalend,
    DateVDetails
  }
};
</script>
<style>
.dateV-wrapper {
  width: 100%;
  height: 100%;
  background: #9e9c9c;
  display: flex;
  flex-direction: column;
}
</style>
