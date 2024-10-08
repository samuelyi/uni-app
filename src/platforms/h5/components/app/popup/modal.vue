<template>
  <transition name="uni-fade">
    <uni-modal
      v-show="visible"
      @touchmove.prevent
    >
      <div class="uni-mask" />
      <div class="uni-modal">
        <div
          v-if="title"
          class="uni-modal__hd"
        >
          <strong
            class="uni-modal__title"
            v-text="title"
          />
        </div>
        <textarea
          v-if="editable"
          ref="editContent"
          class="uni-modal__textarea"
          rows="1"
          :placeholder="placeholderText"
          :value="content"
        />
        <div
          v-else
          class="uni-modal__bd"
          @touchmove.stop
          v-text="content"
        />
        <div class="uni-modal__ft">
          <div
            v-if="showCancel"
            :style="{color:cancelColor_}"
            class="uni-modal__btn uni-modal__btn_default"
            @click="_close('cancel')"
          >
            {{ cancelText }}
          </div>
          <div
            :style="{color:confirmColor}"
            class="uni-modal__btn uni-modal__btn_primary"
            @click="_close('confirm')"
          >
            {{ confirmText }}
          </div>
        </div>
      </div>
      <keypress
        :disable="!visible"
        @esc="_close('cancel')"
        @enter="!editable && _close('confirm')"
      />
    </uni-modal>
  </transition>
</template>
<script>
import transition from './mixins/transition'
import keypress from '../../../helpers/keypress'
import { onThemeChange, offThemeChange, getTheme } from '../../theme'

const ModalTheme = {
  light: {
    cancelColor: '#000000'
  },
  dark: {
    cancelColor: 'rgb(170, 170, 170)'
  }
}
function setCancelColor (theme) {
  this.cancelColor_ = ModalTheme[theme].cancelColor
}

export default {
  name: 'Modal',
  components: { keypress },
  mixins: [transition],
  props: {
    title: {
      type: String,
      default: ''
    },
    content: {
      type: String,
      default: ''
    },
    showCancel: {
      type: Boolean,
      default: true
    },
    cancelText: {
      type: String,
      default: 'Cancel'
    },
    cancelColor: {
      type: String,
      default: '#000000'
    },
    confirmText: {
      type: String,
      default: 'OK'
    },
    confirmColor: {
      type: String,
      default: '#007aff'
    },
    visible: {
      type: Boolean,
      default: false
    },
    editable: {
      type: Boolean,
      default: false
    },
    placeholderText: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      cancelColor_: '#000'
    }
  },
  watch: {
    visible (visible) {
      if (visible) {
        this.cancelColor_ = this.$parent.showModal.cancelColor
        // #000 by default in protocols
        if (this.$parent.showModal.cancelColor === '#000') {
          if (getTheme() === 'dark') this._onThemeChange({ theme: 'dark' })
          onThemeChange(this._onThemeChange)
        }
      } else {
        offThemeChange(this._onThemeChange)
      }
    }
  },
  methods: {
    _onThemeChange ({ theme }) {
      setCancelColor.call(this, theme)
    },
    _close (type) {
      const res = {
        [type]: true
      }
      if (this.editable && type === 'confirm') {
        res.content = this.$refs.editContent.value
      }
      this.$emit('close', res)
    }
  }
}
</script>
<style>
uni-modal {
  position: fixed;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
  z-index: 999;
  display: block;
  box-sizing: border-box;
}

uni-modal .uni-modal {
  position: fixed;
  z-index: 999;
  width: 80%;
  max-width: 300px;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: #ffffff;
  text-align: center;
  border-radius: 3px;
  overflow: hidden;
}

uni-modal .uni-modal * {
  box-sizing: border-box;
}

uni-modal .uni-modal__hd {
  padding: 1em 1.6em 0.3em;
}

uni-modal .uni-modal__title {
  font-weight: 400;
  font-size: 18px;
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
  overflow: hidden;
  text-overflow: ellipsis;
  display: -webkit-box;
  -webkit-line-clamp: 2;
  -webkit-box-orient: vertical;
}

uni-modal .uni-modal__bd {
  padding: 1.3em 1.6em 1.3em;
  min-height: 40px;
  font-size: 15px;
  line-height: 1.4;
  word-wrap: break-word;
  word-break: break-all;
  white-space: pre-wrap;
  color: #999999;
  max-height: 400px;
  overflow-x: hidden;
  overflow-y: auto;
}

uni-modal .uni-modal__textarea {
  resize: none;
  border: 0;
  margin: 0;
  width: 90%;
  padding: 10px;
  font-size: 20px;
  outline: none;
  border: none;
  background-color: #eee;
  text-decoration: inherit;
}

uni-modal .uni-modal__ft {
  position: relative;
  line-height: 48px;
  font-size: 18px;
  display: flex;
}

uni-modal .uni-modal__ft:after {
  content: " ";
  position: absolute;
  left: 0;
  top: 0;
  right: 0;
  height: 1px;
  border-top: 1px solid #d5d5d6;
  color: #d5d5d6;
  transform-origin: 0 0;
  transform: scaleY(0.5);
}

uni-modal .uni-modal__btn {
  display: block;
  flex: 1;
  color: #3cc51f;
  text-decoration: none;
  -webkit-tap-highlight-color: rgba(0, 0, 0, 0);
  position: relative;
  cursor: pointer;
}

uni-modal .uni-modal__btn:active {
  background-color: #eeeeee;
}

uni-modal .uni-modal__btn:after {
  content: " ";
  position: absolute;
  left: 0;
  top: 0;
  width: 1px;
  bottom: 0;
  border-left: 1px solid #d5d5d6;
  color: #d5d5d6;
  transform-origin: 0 0;
  transform: scaleX(0.5);
}

uni-modal .uni-modal__btn:first-child:after {
  display: none;
}

uni-modal .uni-modal__btn_default {
  color: #353535;
}

uni-modal .uni-modal__btn_primary {
  color: #007aff;
}

@media (prefers-color-scheme: dark) {
  uni-modal .uni-modal {
    color: var(--UI-FG-0);
    background-color: var(--UI-BG-2);
  }

  uni-modal .uni-modal__bd {
    color: var(--UI-FG-1);
  }

  uni-modal .uni-modal__btn:active {
    color: rgb(170, 170, 170);
    background-color: var(--UI-BG-COLOR-ACTIVE);
  }

  uni-modal .uni-modal__ft:after,
  uni-modal .uni-modal__btn:after {
    color: var(--UI-BORDER-COLOR-1);
    border-color: var(--UI-BORDER-COLOR-1);
  }
}
</style>
