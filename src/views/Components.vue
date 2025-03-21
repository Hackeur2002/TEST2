<template>
    <div>
        <hero></hero>
        <event-list></event-list>
    </div>
</template>
<script>
import Hero from "./components/Hero";
import EventList from "../components/EventList.vue";

export default {
  name: "components",
  components: {
    Hero,
    EventList
  }
};
</script>
