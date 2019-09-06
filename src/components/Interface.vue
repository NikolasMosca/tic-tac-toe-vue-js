<template>
  <div class="interface">
    <div>
        <strong>X</strong> {{xScore}}
    </div>
    <github-button 
        href="https://github.com/NikolasMosca/tic-tac-toe-vue-js" 
        data-icon="octicon-star" 
        data-size="large" 
        data-show-count="false" 
        aria-label="Star NikolasMosca/tic-tac-toe-vue-js on GitHub"
        class="github-button"
    >
        Star
    </github-button>
    <div>
        <strong>O</strong> {{oScore}}
    </div>
  </div>
</template>

<script>
    import GithubButton from 'vue-github-button'
    export default {
        name: 'Interface',
        props: {
            xScore: Number,
            oScore: Number
        },
        components: {
            GithubButton
        },
    }
</script>

<style lang="scss" scoped>
    .interface {
        display: flex;
        flex-direction: column;
        justify-content: space-between;
        color: white;
        font: {
            family: sans-serif;
            size: 100px;
        }

        .github-button {
            span {
                .widget {
                    display: block !important;
                }
            }
            
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

    @media screen and (max-height: 600px) {
        .interface {
            font-size: 50px;
        }
    }
</style>
