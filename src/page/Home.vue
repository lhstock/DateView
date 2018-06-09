<template>
  <div>
    <div class="date">
      <DateView></DateView>
    </div>
    home

  </div>
</template>
<script>
import DateView from "@/components/DateView";
export default {
  name: "home",
  components: {
    DateView
  }
};
</script>
<style>
.date {
  width: 100vw;
  height: 600px;
}
</style>

