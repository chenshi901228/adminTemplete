<template>
  <el-submenu class="navTab" v-if="menu.children && menu.children.length >= 1" :index="menu.id" :popper-append-to-body="false">
    <template slot="title">
      <svg class="icon-svg aui-sidebar__menu-icon" aria-hidden="true"><use :xlink:href="`#${menu.icon}`"></use></svg>
      <span>{{ menu.name }}</span>
    </template>
    <sub-menu v-for="item in menu.children" :key="item.id" :menu="item"></sub-menu>
  </el-submenu>
  <el-menu-item v-else-if="menu.isShow" :index="menu.id" ref="li">
    <a
      :href="isBrowserTabOpen(menu.id) ? getBrowserTabOpenURL(menu.id) : 'javascript:;'"
      :target="isBrowserTabOpen(menu.id) ? '_blank' : '_self'"
      @click="isBrowserTabOpen(menu.id) ? 'return false;' : gotoRouteHandle(menu.id)">
      <svg class="icon-svg aui-sidebar__menu-icon" aria-hidden="true"><use :xlink:href="`#${menu.icon}`"></use></svg>
      <span>{{ menu.name }}</span>
    </a>
  </el-menu-item>
</template>

<script>
import SubMenu from './main-sidebar-sub-menu'
export default {
  name: 'sub-menu',
  data () {
    return {
      browserTabOpenList: [
        '1067246875800000042',
        '1340949288542347268'
      ]
    }
  },
  props: {
    menu: {
      type: Object,
      required: true
    }
  },
  components: {
    SubMenu
  },
  created () {
    this.$nextTick(() => {
      if (this.$refs.li) {
        let $li = this.$refs.li.$el
        let $a = $li.firstElementChild
        if ($a) {
          let pl = '0', pr = '0'
          if ($li.currentStyle) {
            pl = $li.currentStyle['paddingLeft']
            pr = $li.currentStyle['paddingRight']
          } else {
            pl = window.document.defaultView.getComputedStyle($li, null)['paddingLeft']
            pr = window.document.defaultView.getComputedStyle($li, null)['paddingRight']
          }
          $li.setAttribute('style', `padding-left: 0; padding-right: 0;`)
          $a.setAttribute('style', `padding-left: ${pl}; padding-right: ${pr};`)
        }
      }
    })
  },
  methods: {
    // 是否通过浏览器Tab打开菜单
    isBrowserTabOpen (menuId) {
      return this.browserTabOpenList.filter(item => item === menuId).length >= 1;
    },
    // 获取浏览器Tab打开菜单URL
    getBrowserTabOpenURL (menuId) {
      var route = window.SITE_CONFIG['dynamicMenuRoutes'].filter(item => item.meta.menuId === menuId)[0]
      return route ? route.meta.iframeURL : '';
    },
    // 通过menuId与动态(菜单)路由进行匹配跳转至指定路由
    gotoRouteHandle (menuId) {
      var route = window.SITE_CONFIG['dynamicMenuRoutes'].filter(item => item.meta.menuId === menuId)[0]
      if (route) {
        if(route.name == "liveRoom") {
          let t = this.$router.resolve({name: route.name})
          window.open(t.href, "_blank")
        }else {
          this.$router.push({ name: route.name })
        }
      }
    }
  }
}
</script>

<style lang="scss">
.aui-sidebar__menu {
  .el-menu-item > a {
    display: block;
    color: inherit;
    text-decoration: none;
  }
}
.navTab{
  .el-menu-item.is-active {
    color: #fff !important;
    background-color: #2e3b42 !important;
  }
}
</style>
