<template>
    <div>{{ formattedTime() }}</div>
</template>
  
<script>
  export default {
    props: {
      countdown: {
        type: Number,
        required: true,
      },
    },
    methods: {
      formattedTime() {
        if(this.countdown != 0) {
            this.intitial = false
            let hours = Math.floor(this.countdown / 3600);
            let minutes = Math.floor((this.countdown % 3600) / 60);
            let seconds = this.countdown % 60;
            return `${String(hours).padStart(2, '0')}:${String(minutes).padStart(2, '0')}:${String(seconds).padStart(2, '0')}`;
        } else {
            return 'Готово'
        }
      }
    }
}
</script>