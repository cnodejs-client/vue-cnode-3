<template>
  <div class="door-wrap">
    <div class="door__row">
      <div class="door__logo">
        <img src="../assets/logo.png" alt="logo">
      </div>
      <div class="door__loader">
        <CircleLoading :state="circleState" @loaded="loaded"></CircleLoading>
      </div>
    </div>
  </div>
</template>

<script>
  import Tools from '../js/tools';
  import CircleLoading from './CircleLoading';

  export default {
    data() {
      return {
        animated: false,
        circleState: 'init',
        animEventName: ''
      };
    },
    components: {
      CircleLoading
    },
    props: {
      state: {
        type: String,
        default: 'hide'
      }
    },
    watch: {
      state(newVal) {
        this.circleState = newVal;
      }
    },
    mounted() {
      this.animEventName = Tools.getAnimEventName();

      const endAnimation = () => {
        if (this.animEventName) {
          this.$el.removeEventListener(this.animEventName, endAnimation);
        }

        this.$emit('ready');
      };

      this.$el.classList.add('loading');

      if (this.animEventName) {
        this.$el.addEventListener(this.animEventName, endAnimation, false);
      } else {
        endAnimation();
      }
    },
    methods: {
      loaded() {
        this.$el.classList.remove('loading');
        this.$el.classList.add('loaded');
      }
    }
  };
</script>

<style rel="stylesheet/scss" lang="scss" scoped>
  .door-wrap {
    position: fixed;
    top: 0;
    left: 0;
    display: table;
    width: 100%;
    height: 100%;
    background: #f1f1f1;
    z-index: 1002;

    &.loading,
    &.loaded {
      .door__logo,
      .door__loader {
        opacity: 1;
      }

      .door__loader {
        animation-delay: .2s;
      }
    }

    &.loading {
      .door__logo,
      .door__loader {
        animation: initialAnim 1s cubic-bezier(0.7,0,0.3,1) both;
      }
    }

    &.loaded {
      animation: loadedWrapAnim 1s cubic-bezier(0.7,0,0.3,1) forwards;

      .door__logo,
      .door__loader {
        animation: loadedAnim .5s cubic-bezier(0.7,0,0.3,1) forwards;
      }
    }
  }

  .door {
    &__row {
      display: table-cell;
      text-align: center;
      vertical-align: middle;
    }

    &__logo,
    &__loader {
      opacity: 0;
    }

    &__loader {
      margin-top: 2rem;
      animation-delay: .2s;
    }
  }

  @keyframes initialAnim {
    from {
      opacity: 0;
      transform: translate3d(0,800px,0);
    }
  }

  @keyframes loadedAnim {
    to {
      opacity: 0;
      transform: translate3d(0,-100%,0) scale3d(0.3,0.3,1);
    }
  }

  @keyframes loadedWrapAnim {
    to {
      transform: translate3d(0,-100%,0);
    }
  }
</style>
