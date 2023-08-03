<template>
  <div style="display: flex;width:100%">
    <div :class="{'has-logo':showLogo}"
         :style="{ backgroundColor: settings.sideTheme === 'theme-dark' ? variables.menuBackground : variables.menuLightBackground, }"
         style="width:10%">
      <logo v-if="showLogo"
            :collapse="isCollapse" />
    </div>
    <div class="navbar">
      <top-nav id="topmenu-container"
               class="topmenu-container"
               v-if="topNav" />

      <el-menu :default-active="activeMenu"
               mode="horizontal"
               :background-color="settings.sideTheme === 'theme-dark' ? variables.menuBackground : variables.menuLightBackground"
               :text-color="settings.sideTheme === 'theme-dark' ? variables.menuColor : variables.menuLightColor"
               :unique-opened="true"
               :active-text-color="settings.theme">
        <sidebar-item v-for="(route, index) in sidebarRouters"
                      :key="route.path  + index"
                      :item="route"
                      :base-path="route.path" />
      </el-menu>
      <div class="right-menu">
        <el-dropdown class="avatar-container right-menu-item hover-effect"
                     trigger="click">
          <div class="avatar-wrapper">
            <img :src="avatar"
                 class="user-avatar">
            <i class="el-icon-caret-bottom" />
          </div>
          <el-dropdown-menu slot="dropdown">
            <router-link to="/user/profile">
              <el-dropdown-item>个人中心</el-dropdown-item>
            </router-link>
            <el-dropdown-item @click.native="setting = true">
              <span>布局设置</span>
            </el-dropdown-item>
            <el-dropdown-item divided
                              @click.native="logout">
              <span>退出登录</span>
            </el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </div>
    </div>
  </div>
</template>

<script>
import { mapGetters, mapState } from 'vuex'
import TopNav from '@/components/TopNav'
import Logo from "@/layout/components/Sidebar/Logo";
import SidebarItem from "../components/Sidebar/SidebarItem";
import variables from "@/assets/styles/variables.scss";

export default {
  components: {
    TopNav,
    SidebarItem,
    Logo
  },
  computed: {
    ...mapState(["settings"]),
    ...mapGetters([
      'sidebar',
      'avatar',
      'device',
      "sidebarRouters"
    ]),
    activeMenu () {
      const route = this.$route;
      const { meta, path } = route;
      // if set path, the sidebar will highlight the path you set
      if (meta.activeMenu) {
        return meta.activeMenu;
      }
      return path;
    },
    showLogo () {
      return this.$store.state.settings.sidebarLogo;
    },
    variables () {
      return variables;
    },
    isCollapse () {
      return false;
    },
    setting: {
      get () {
        return this.$store.state.settings.showSettings
      },
      set (val) {
        this.$store.dispatch('settings/changeSetting', {
          key: 'showSettings',
          value: val
        })
      }
    },
    topNav: {
      get () {
        return this.$store.state.settings.topNav
      }
    }
  },
  methods: {
    toggleSideBar () {
      this.$store.dispatch('app/toggleSideBar')
    },
    async logout () {
      this.$confirm('确定注销并退出系统吗？', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$store.dispatch('LogOut').then(() => {
          location.href = '/index';
        })
      }).catch(() => { });
    }
  }
}
</script>

<style lang="scss" scoped>
::v-deep .el-submenu__icon-arrow {
  display: none;
}
::v-deep .el-menu--popup-bottom-start {
  margin-top: 0 !important;
}
::v-deep .el-menu--horizontal {
  width: 100%;
  left: 0px;
}
.navbar {
  width: 100%;
  height: 56px;
  overflow: hidden;
  position: relative;
  background: #fff;
  box-shadow: 0 1px 4px rgba(0, 21, 41, 0.08);

  .hamburger-container {
    line-height: 46px;
    height: 100%;
    float: left;
    cursor: pointer;
    transition: background 0.3s;
    -webkit-tap-highlight-color: transparent;

    &:hover {
      background: rgba(0, 0, 0, 0.025);
    }
  }

  .breadcrumb-container {
    float: left;
  }

  .topmenu-container {
    position: absolute;
    left: 56px;
  }

  .errLog-container {
    display: inline-block;
    vertical-align: top;
  }

  .el-menu {
    display: flex;
    height: 56px;
    div {
      width: auto;
      li {
        padding: 0 15px !important;
      }
      a {
        li.el-menu-item {
          height: 56px !important;
          line-height: 56px !important;
          padding: 0 15px;
        }
      }
    }
  }

  .right-menu {
    height: 100%;
    line-height: 56px;
    position: absolute;
    right: 0;
    top: 0px;

    &:focus {
      outline: none;
    }

    .right-menu-item {
      display: inline-block;
      padding: 0 8px;
      height: 100%;
      font-size: 18px;
      color: #5a5e66;
      vertical-align: text-bottom;

      &.hover-effect {
        cursor: pointer;
        transition: background 0.3s;

        &:hover {
          background: rgba(0, 0, 0, 0.025);
        }
      }
    }

    .avatar-container {
      margin-right: 30px;

      .avatar-wrapper {
        margin-top: 5px;
        position: relative;

        .user-avatar {
          cursor: pointer;
          width: 40px;
          height: 40px;
          border-radius: 10px;
        }

        .el-icon-caret-bottom {
          cursor: pointer;
          position: absolute;
          right: -20px;
          top: 25px;
          font-size: 12px;
        }
      }
    }
  }
}
</style>
