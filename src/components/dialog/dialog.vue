<template>
  <transition name="cube-dialog-fade">
    <cube-popup type="dialog" :mask="true" :center="true" v-show="isVisible">
      <div class="cube-dialog-main">
        <span class="cube-dialog-close" v-show="showClose" @click="close"><i class="cubeic-close"></i></span>
        <div :class="containerClass">
          <p class="cube-dialog-icon" v-if="icon"><i :class="icon"></i></p>
          <h2 v-if="title || $slots.title" class="cube-dialog-title">
            <slot name="title">
              <p class="cube-dialog-title-def">{{title}}</p>
            </slot>
          </h2>
          <div class="cube-dialog-content">
            <slot name="content">
              <div class="cube-dialog-content-def">
                <p v-html="content"></p>
              </div>
            </slot>
          </div>
          <div class="cube-dialog-btns" :class="{'border-right-1px': isConfirm}">
            <slot name="btns">
              <a :href="_cancelBtn.href" class="cube-dialog-btn border-top-1px" :class="{'cube-dialog-btn_highlight': _cancelBtn.active, 'cube-dialog-btn_disabled': _cancelBtn.disabled}" v-if="isConfirm" @click="cancel">{{_cancelBtn.text}}</a>
              <a :href="_confirmBtn.href" class="cube-dialog-btn border-top-1px" :class="{'cube-dialog-btn_highlight': _confirmBtn.active, 'cube-dialog-btn_disabled': _confirmBtn.disabled}" @click="confirm">{{_confirmBtn.text}}</a>
            </slot>
          </div>
        </div>
      </div>
    </cube-popup>
  </transition>
</template>

<script type="text/ecmascript-6">
  import CubePopup from '../popup/popup.vue'
  import apiMixin from '../../common/mixins/api'

  const COMPONENT_NAME = 'cube-dialog'
  const EVENT_CONFIRM = 'confirm'
  const EVENT_CANCEL = 'cancel'
  const EVENT_CLOSE = 'close'

  const defHref = 'javascript:;'
  const defConfirmBtn = {
    text: '确定',
    active: true,
    disabled: false,
    href: defHref
  }
  const defCancelBtn = {
    text: '取消',
    active: false,
    disabled: false,
    href: defHref
  }
  const parseBtn = (btn, defBtn) => {
    if (typeof btn === 'string') {
      btn = {
        text: btn
      }
    }
    return Object.assign({}, defBtn, btn)
  }

  export default {
    name: COMPONENT_NAME,
    mixins: [apiMixin],
    props: {
      type: {
        type: String,
        default: 'alert'
      },
      icon: {
        type: String,
        default: ''
      },
      title: {
        type: String,
        default: ''
      },
      content: {
        type: String,
        default: ''
      },
      showClose: {
        type: Boolean,
        default: false
      },
      confirmBtn: {
        type: [Object, String],
        default() {
          return {
            ...defConfirmBtn
          }
        }
      },
      cancelBtn: {
        type: [Object, String],
        default() {
          return {
            ...defCancelBtn
          }
        }
      }
    },
    data() {
      return {
        defHref
      }
    },
    computed: {
      _confirmBtn() {
        return parseBtn(this.confirmBtn, defConfirmBtn)
      },
      _cancelBtn() {
        return parseBtn(this.cancelBtn, defCancelBtn)
      },
      isConfirm() {
        return this.type === 'confirm'
      },
      containerClass() {
        return `cube-dialog-${this.type}`
      }
    },
    methods: {
      confirm(e) {
        if (this._confirmBtn.disabled) {
          return
        }
        this.hide()
        this.$emit(EVENT_CONFIRM, e)
      },
      cancel(e) {
        if (this._cancelBtn.disabled) {
          return
        }
        this.hide()
        this.$emit(EVENT_CANCEL, e)
      },
      close(e) {
        this.hide()
        this.$emit(EVENT_CLOSE, e)
      }
    },
    components: {
      CubePopup
    }
  }
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @require "../../common/stylus/variable.styl"
  @require "../../common/stylus/mixin.styl"

  .cube-dialog-main
    width: 270px
    padding: 0
    text-align: center
    overflow: hidden
    border-radius: 2px
    background-color: $dialog-bgc
  .cube-dialog-confirm, .cube-dialog-alert
    position: relative
    overflow: hidden
  .cube-dialog-icon
    margin-top: 20px
    margin-bottom: 16px
    line-height: 1
    color: $dialog-icon-color
    font-size: $fontsize-large-xxxx
    i
      display: inline-block
      width: 30px
      height: 30px
      padding: 10px
      border-radius: 50%
      background-color: $dialog-icon-bgc
    +
      .cube-dialog-title
        .cube-dialog-title-def
          margin-top: 0
    +
      .cube-dialog-content
        margin-top: -4px
  .cube-dialog-title
    color: $dialog-title-color
    font-size: $fontsize-large
    line-height: 1
    +
      .cube-dialog-content
        margin-top: 12px
  .cube-dialog-title-def
    margin: 30px 16px 0
    overflow: hidden
    white-space: nowrap
  .cube-dialog-content
    margin: 16px 0
    text-align: left
    color: $dialog-color
    font-size: $fontsize-medium
    line-height: 22px
  .cube-dialog-content-def
    padding: 0 16px
    > p
      display: table
      margin: auto
  .cube-dialog-confirm
    .cube-dialog-btns
      .cube-dialog-btn
        width: 50%
        float: left
      &.border-right-1px
        &::after
          right: 50%
          border-color: $dialog-btns-split-color
  .cube-dialog-close
    display: flex
    align-items: center
    justify-content: center
    z-index: 1
    position: absolute
    top: 0
    right: 0
    width: 32px
    height: 32px
    color: $dialog-close-color
    font-size: $fontsize-large-x
  .cube-dialog-btns
    overflow: hidden
    width: 100%
    font-size: 0
  .cube-dialog-btn
    display: inline-block
    width: 100%
    padding: 17px 10px
    margin: 0
    font-size: $fontsize-large
    line-height: 1
    text-align: center
    text-decoration: none
    color: $dialog-btn-color
    background-color: $dialog-btn-bgc
    background-clip: padding-box
    box-sizing: border-box
    &:active
      background-color: $dialog-btn-active-bgc
  .cube-dialog-btn_highlight
    color: $dialog-btn-highlight-color
    &:active
      background-color: $dialog-btn-highlight-active-bgc
  .cube-dialog-btn_disabled
    color: $dialog-btn-disabled-color
    &:active
      background-color: $dialog-btn-disabled-active-bgc

  .cube-dialog-fade-enter-active
    animation: dialog-fadein .4s
    .cube-dialog-main
      animation: dialog-zoom .4s

  @keyframes dialog-fadein
    0%
      opacity: 0
    100%
      opacity: 1
  @keyframes dialog-zoom
    0%
      transform: scale(0)
    50%
      transform: scale(1.1)
    100%
      transform: scale(1)
</style>
