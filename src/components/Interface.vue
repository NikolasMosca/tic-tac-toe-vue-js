<template>
  <div class="interface">
    <div>
        <strong>X</strong> {{xScore}}
    </div>
    <div>
        <strong>O</strong> {{oScore}}
    </div>
  </div>
</template>

<script>
  export default {
    name: 'Interface',
    props: {
        xScore: Number,
        oScore: Number
    }
  }
</script>

<style lang="scss" scoped>
    .interface {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        font: {
            family: sans-serif;
            size: 100px;
        }
    }

    @media screen and (orientation:portrait) {
        .interface {
            width: 80%;
            flex-direction: row;
            font-size: 50px;
        }
    }
    @media screen and (orientation:landscape) {
        .interface {
            height: 80%;
        }
    }
</style>
