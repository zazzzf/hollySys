<template>
  <transition name="showHeader">
    <div v-if="visible" class="header-animat">
      <a-layout-header
        v-if="visible"
        :class="[fixedHeader && 'ant-header-fixedHeader', sidebarOpened ? 'ant-header-side-opened' : 'ant-header-side-closed', ]"
        :style="{ padding: '0' }">
        <h1 class="newHeaderTop" >和利时综合能源管理系统 <span >{{nowTime}}</span> </h1>
      </a-layout-header>
    </div>
  </transition>
</template>

<script>
import UserMenu from '../tools/UserMenu'
import SMenu from '../Menu/'
import Logo from '../tools/Logo'
import { mixin } from '@/utils/mixin'

export default {
  name: 'GlobalHeader',
  components: {
    UserMenu,
    SMenu,
    Logo
  },
  mixins: [mixin],
  props: {
    mode: {
      type: String,
      // sidemenu, topmenu
      default: 'sidemenu'
    },
    menus: {
      type: Array,
      required: true
    },
    theme: {
      type: String,
      required: false,
      default: 'dark'
    },
    collapsed: {
      type: Boolean,
      required: false,
      default: false
    },
    device: {
      type: String,
      required: false,
      default: 'desktop'
    }
  },
  data () {
    return {
      visible: true,
      oldScrollTop: 0,
      nowTime:'',
    }
  },
  mounted () {
    document.addEventListener('scroll', this.handleScroll, { passive: true })
    setInterval(() => {
      const year = new Date().getFullYear()
      const month = new Date().getMonth() + 1
      const day = new Date().getDate()
      const hours = new Date().getHours()
      const minutes = new Date().getMinutes()
      var ss = (new Date().getTime() % 60000)
      const seconds = (ss - (ss % 1000)) / 1000
      var daylist = ['星期日', '星期一', '星期二', '星期三', '星期四', '星期五', '星期六']
      const week = daylist[new Date().getDay()]
      this.nowTime = `${year}-${month<10? '0'+ month: month}-${day <10? '0'+ day: day} ${hours <10? '0'+ hours: hours}:${minutes <10? '0'+ minutes: minutes}`
    }, 1000)
  },
  methods: {
    handleScroll () {
      if (!this.autoHideHeader) {
        return
      }

      const scrollTop = document.body.scrollTop + document.documentElement.scrollTop
      if (!this.ticking) {
        this.ticking = true
        requestAnimationFrame(() => {
          if (this.oldScrollTop > scrollTop) {
            this.visible = true
          } else if (scrollTop > 300 && this.visible) {
            this.visible = false
          } else if (scrollTop < 300 && !this.visible) {
            this.visible = true
          }
          this.oldScrollTop = scrollTop
          this.ticking = false
        })
      }
    },
    toggle () {
      this.$emit('toggle')
    }
  },
  beforeDestroy () {
    document.body.removeEventListener('scroll', this.handleScroll, true)
  }
}
</script>

<style lang="less">
@import '../index.less';

.header-animat{
  position: relative;
  z-index: @ant-global-header-zindex;
}
.showHeader-enter-active {
  transition: all 0.25s ease;
}
.showHeader-leave-active {
  transition: all 0.5s ease;
}
.showHeader-enter, .showHeader-leave-to {
  opacity: 0;
}
.newHeaderTop{
  color:#fff;text-align:center;
  font-size: 20px;
  font-weight: bold;
  span{
    float:right;margin-right:20px;font-size: 14px; font-weight: 500;color: #cdcdcd
  }
}
</style>
