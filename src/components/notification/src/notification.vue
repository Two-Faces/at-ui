<template>
  <transition name="notification-fade" @after-leave="doDestory">
    <div
      class="at-notification"
      :class="{
        [`at-notification--${type}`]: type,
        'at-notification--with-message': message,
        'at-notification--hover': !showClose
      }"
      :style="{ top: top ? top + 'px' : 'auto' }"
      v-show="isShow"
      @click="!showClose && handleClose('click')"
      @mouseleave="startTimer"
      @mouseenter="clearTimer">
      <i class="at-notification__icon" :class="iconClass" v-if="showIcon"></i>
      <div class="at-notification__content">
        <p class="at-notification__title" v-if="title" v-text="title"></p>
        <p class="at-notification__message" v-if="message" v-text="message"></p>
      </div>
      <i class="icon icon-x at-notification__close" v-show="showClose" @click="handleClose('times')"></i>
    </div>
  </transition>
</template>

<script>
export default {
  data () {
    return {
      type: '',
      title: '',
      message: '',
      isShow: false,
      duration: 4000,
      icon: '',
      showClose: true,
      onClose: null,
      top: null,
      timer: null,
      closed: false
    }
  },
  watch: {
    closed (val) {
      if (val) {
        this.isShow = false
      }
    }
  },
  computed: {
    iconClass () {
      const classArr = {
        'success': 'icon icon-check-circle',
        'error': 'icon icon-x-circle',
        'warning': 'icon icon-alert-circle',
        'info': 'icon icon-info'
      }
      return this.icon || classArr[this.type]
    },
    showIcon () {
      return this.type
    }
  },
  methods: {
    doDestory () {
      this.$destroy(true)
      this.$el.parentNode.removeChild(this.$el)
    },
    handleClose (event) {
      this.closed = true
      if (typeof this.onClose === 'function') {
        this.onClose(event)
      }
    },
    startTimer () {
      if (this.duration > 0) {
        this.timer = setTimeout(() => {
          if (!this.closed) {
            this.handleClose('timer')
          }
        }, this.duration)
      }
    },
    clearTimer () {
      clearTimeout(this.timer)
    }
  },
  mounted () {
    this.startTimer()
  }
}
</script>
