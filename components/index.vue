<template>
<div class="number-counter" :data-mode="mode">
  <div :style="{ width, height, perspective }" class="_numbers" ref="$numbers">
    <div :style="{ fontSize }" class="background-number">
      <template v-if="mode === 'up'">
        <div class="current">
          <div class="flip-row">
            <div class="flip-col">
              <span>{{ value }}</span>
            </div>
          </div>
        </div>
        <div class="next">
          <div class="flip-row">
            <div class="flip-col">
              <span>{{ value + (mode === 'up' ? 1 : -1) | next }}</span>
            </div>
          </div>
        </div>
      </template>
      <template v-else>
        <div class="next">
          <div class="flip-row">
            <div class="flip-col">
              <span>{{ value + (mode === 'up' ? 1 : -1) | next }}</span>
            </div>
          </div>
        </div>
        <div class="current">
          <div class="flip-row">
            <div class="flip-col">
              <span>{{ value }}</span>
            </div>
          </div>
        </div>
      </template>
    </div>

    <div :style="{ width, fontSize }" class="_number" data-number="1">
      <template v-if="mode === 'up'">
        <div :style="{ height: halfHeight }" class="flip-cover-s">
          <div class="flip-row">
            <div class="flip-col">
              <span>{{ value }}</span>
            </div>
          </div>
        </div>
        <div :style="{ height: halfHeight }" class="flip-cover-e">
          <div class="flip-row front">
            <div class="flip-col">
              <span>{{ value }}</span>
            </div>
          </div>
          <div class="flip-row back">
            <div class="flip-col">
              <span>{{ value + (mode === 'up' ? 1 : -1) | next }}</span>
            </div>
          </div>
        </div>
      </template>
      <template v-else>
        <div :style="{ height: halfHeight }" class="flip-cover-e">
          <div class="flip-row front">
            <div class="flip-col">
              <span>{{ value }}</span>
            </div>
          </div>
          <div class="flip-row back">
            <div class="flip-col">
              <span>{{ value + (mode === 'up' ? 1 : -1) | next }}</span>
            </div>
          </div>
        </div>
        <div :style="{ height: halfHeight }" class="flip-cover-s">
          <div class="flip-row">
            <div class="flip-col">
              <span>{{ value }}</span>
            </div>
          </div>
        </div>
      </template>
    </div>
  </div>
</div>
</template>

<script>
export default {
  name: 'FlipCounter',

  data() {
    return {
      _interval: null
    }
  },

  model: {
    prop: 'value'
  },

  props: {
    value: Number,

    interval: {
      type: [ Number, String ],
      default: 1000
    },

    mode: {
      type: String,
      default: 'up'
    },

    size: {
      type: [String, Number],
      default: 75,
      validator(value) {
        return !isNaN(value);
      }
    }
  },

  computed: {
    _size() { return Number(this.size); },
    width() { return `${this.size}px`; },
    height() { return `${this.size * 1.5}px`; },
    halfHeight() { return `${this.size * 1.5 / 2}px`; },
    perspective() { return `${this.size * 5}px`; },
    fontSize() { return `${this.size * 1.2}px`; }
  },

  methods: {
    checkRange(value) {
      if(value < 0) value = 9;
      else if(value > 9) value = 0;

      return value;
    },

    increase() {
      /** @type {HTMLElement} */
      const $numbers = this.$refs.$numbers;

      if($numbers instanceof HTMLElement) {
        $numbers.classList.add('active');
        let nextValue = this.checkRange(this.value + (this.mode === 'up' ? 1 : -1));
  
        setTimeout(() => {
          $numbers.classList.remove('active');
          this.$emit('input', nextValue);
        }, 500);
      }
    }
  },

  filters: {
    next(value) {
      if(value < 0) value = 9;
      else if(value > 9) value = 0;

      return value;
    }
  },

  created() {
    this._interval = setInterval(() => {
      this.increase();
    }, Number(this.interval));
  },

  destroyed() {
    clearInterval(this._interval);
  }
}
</script>

<style lang="scss" scoped>
$width: 75px;
$height: $width * 1.5;
$sec: 500ms;


@font-face {
  font-family: 'Helvetica Neue';
  font-style: normal;
  font-weight: normal;
  src: local('Helvetica 43 Light Extended'), url('../assets/HelveticaNeue-LightExt.woff') format('woff');
}

.number-counter {
  position: relative;
  
  ._numbers {
    position: relative;
    margin: 0 10px;
    display: inline-block;
    width: $width;
    height: $height;
    background: #eee;
    border-radius: 4px;
    perspective: $width * 5;

    .background-number {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: #3A3A3A;
      border-radius: 4px;
      text-align: center;
      font-size: $width * 1.2;
      color: #D2D2D2;

      .current {
        position: relative;
        height: 50%;
        border-top-left-radius: 4px;
        border-top-right-radius: 4px;
        overflow: hidden;

        .flip-row {
          top: 0;
          bottom: 0;

          .flip-col {
            margin-top: 2%;
          }
        }
      }

      .next {
        position: relative;
        height: 50%;
        border-bottom-left-radius: 4px;
        border-bottom-right-radius: 4px;
        overflow: hidden;

        .flip-row {
          top: 0;
          bottom: 0;

          .flip-col {
            margin-top: -73%;
          }
        }
      }
    }

    ._number {
      position: relative;
      margin: 0 auto;
      width: $width;
      height: 100%;
      font-size: $width * 1.2;
      color: #D2D2D2;

      &:before, &:after {
        position: absolute;
        display: block;
        width: 100%;
        height: 1px;
      }

      &:after {
        bottom: calc(50% - 1px);
        background: #22222288;
        content: '';
      }

      &:before {
        top: calc(50% - 1px);
        background: #34343488;
        content: '';
      }

      [class*="flip-cover-"] {
        position: relative;
        height: $height / 2;
        text-align: center;
        box-sizing: border-box;
        overflow: hidden;

        &:first-child {
          border-top-left-radius: 4px;
          border-top-right-radius: 4px;

          .flip-row {
            top: 0;
            bottom: 0;

            .flip-col {
              margin-top: 2%;
            }
          }
        }

        &:last-child {
          background: #3A3A3A;
          border-bottom-left-radius: 4px;
          border-bottom-right-radius: 4px;

          .flip-row {
            top: 0;
            bottom: 0;

            .flip-col {
              margin-top: -73%;
            }

            &.front {
              opacity: 1;
            }

            &.back {
              color: #D2D2D2;
              transform: rotateX(180deg);
              opacity: 0;

              .flip-col {
                margin-top: 2%;
              }
            }
          }
        }
      }
    }
  }

  &[data-mode="up"] ._numbers {
    &.active {
      .background-number {
        .current {
          opacity: 0;
          animation: toTransparent $sec ease-in-out 1;
        }
      }

      ._number {
        transform: rotateX(180deg);
        animation: flip $sec ease-in-out 1;

        [class*="flip-cover-"] {
          &:first-child {
            opacity: 0;
            animation: toImmediatelyTransparent $sec ease-in-out 1;

            .flip-row {
              opacity: 0;
              animation: toTransparent $sec ease-in-out 1;
            }
          }

          &:last-child {
            .flip-row {
              &.front {
                opacity: 0;
                animation: toTransparent $sec ease-in-out 1;
              }

              &.back {
                opacity: 1;
                animation: toVisible $sec ease-in-out 1;
              }
            }
          }
        }
      }
    }
  }

  &[data-mode="down"] ._numbers {
    &.active {
      .background-number {
        .current {
          opacity: 0;
          animation: toTransparent $sec ease-in-out 1;
        }
      }

      ._number {
        transform: rotateX(-180deg);
        animation: flipdown $sec ease-in-out 1;

        [class*="flip-cover-"] {
          &:first-child {
            .flip-row {
              &.front {
                opacity: 0;
                animation: toTransparent $sec ease-in-out 1;
              }

              &.back {
                opacity: 1;
                animation: toVisible $sec ease-in-out 1;
              }
            }
          }

          &:last-child {
            opacity: 0;
            animation: toImmediatelyTransparent $sec ease-in-out 1;

            .flip-row {
              opacity: 0;
              animation: toTransparent $sec ease-in-out 1;
            }
          }
        }
      }
    }

    .background-number {
      .current {
        .flip-row {
          .flip-col {
            margin-top: -73%;
          }
        }
      }

      .next {
        .flip-row {
          .flip-col {
            margin-top: 2%;
          }
        }
      }
    }

    ._number {
      [class*="flip-cover-"] {
        &:first-child {
          background: #3A3A3A;

          .flip-row {
            &.front {
              opacity: 1;
            }

            &.back {
              opacity: 0;
              transform: rotateX(-180deg);

              .flip-col {
                margin-top: -73%;
              }
            }
          }
        }

        &:last-child {
          background: transparent;
        }
      }
    }
  }
}

.flip-row {
  position: absolute;
  display: flex;
  width: 100%;
  overflow: hidden;

  .flip-col {
    flex: 1;
    height: 100%;

    span {
      font-family: 'Helvetica Neue' !important;
    }
  }
}

@keyframes flip {
  0% {
    transform: rotateX(0deg);
  }

  100% {
    transform: rotateX(180deg);
  }
}

@keyframes flipdown {
  0% {
    transform: rotateX(0deg);
  }

  100% {
    transform: rotateX(-180deg);
  }
}

@keyframes toImmediatelyTransparent {
  0% {
    opacity: 1;
  }

  0% {
    opacity: 0;
  }

  100% {
    opacity: 0;
  }
}

@keyframes toTransparent {
  0% {
    opacity: 1;
  }

  49% {
    opacity: 1;
  }

  50% {
    opacity: 0;
  }

  100% {
    opacity: 0;
  }
}

@keyframes toVisible {
  0% {
    opacity: 0;
  }

  49% {
    opacity: 0;
  }

  50% {
    opacity: 1;
  }

  100% {
    opacity: 1;
  }
}
</style>
