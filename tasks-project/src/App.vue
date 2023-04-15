<template>

  <!-- <input type="text" ref="inputName" placeholder="input your name"> -->
  <!-- <button @click="handleClick">Click me</button> -->
  <TaskViewer/>

</template>

<script>
import TaskViewer from './components/TaskViewer.vue'

export default {
  name: 'App', // optional
  components: {
    TaskViewer
  },
  methods: {
    handleClick(){
      const inputField = this.$refs.inputName
      alert(inputField.value)
    }
  }
}
</script>

<style>
#app {
  font-family: Helvetica, Arial, sans-serif;
}
</style>
