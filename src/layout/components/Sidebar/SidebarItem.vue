<template>
  <div v-if="!item.hidden">

    <template v-if="hasOneShowingChild(item.children,item) && (!onlyOneChild.children||onlyOneChild.noShowingChildren)&&!item.alwaysShow">
      <app-link v-if="onlyOneChild.meta"
                :to="resolvePath(onlyOneChild.path, onlyOneChild.query)">
        <el-menu-item :index="resolvePath(onlyOneChild.path)"
                      :class="{'submenu-title-noDropdown':!isNest}">
          <item :icon="onlyOneChild.meta.icon||(item.meta&&item.meta.icon)"
                :title="onlyOneChild.meta.title" />
        </el-menu-item>
      </app-link>
    </template>

    <el-submenu v-else
                ref="subMenu"
                :index="resolvePath(item.path)"
                popper-append-to-body>
      <template slot="title">
        <item v-if="item.meta"
              :icon="item.meta && item.meta.icon"
              :title="item.meta.title" />
      </template>
      <div v-for="second in item.children"
           :key="second.path">
        <div v-if="!second.hidden">
          <template v-if="hasOneShowingChild(second.children,second) && (!second.children||second.noShowingChildren)&&!second.alwaysShow">
            <app-link class="first"
                      v-if="second.meta"
                      :to="resolvePath(second.path, second.query)">
              <el-menu-item :index="resolvePath(second.path)"
                            :class="{'submenu-title-noDropdown':!true}">
                <item :icon="second.meta.icon||(second.meta&&second.meta.icon)"
                      :title="second.meta.title" />
              </el-menu-item>
            </app-link>
          </template>

          <el-submenu v-else
                      class="second"
                      ref="subMenu"
                      :index="resolvePath(second.path)"
                      popper-append-to-body>
            <template slot="title">
              <item v-if="second.meta"
                    :icon="second.meta && second.meta.icon"
                    :title="second.meta.title" />
              <!-- <el-menu-item-group title="标题"> -->

              <div v-for="third in second.children"
                   :key="third.path">
                <div v-if="!third.hidden">
                  <template>
                    <app-link class="second"
                              v-if="third.meta"
                              :to="resolvePath(third.path, third.query)">
                      <el-menu-item :index="resolvePath(third.path)"
                                    :class="{'submenu-title-noDropdown':!true}">
                        <item class="third"
                              :icon="third.meta.icon||(third.meta&&child.third.icon)"
                              :title="third.meta.title" />
                      </el-menu-item>
                    </app-link>
                  </template>
                </div>
              </div>
              <!-- </el-menu-item-group> -->
            </template>

          </el-submenu>
        </div>
      </div>

    </el-submenu>
  </div>
</template>

<script>
import path from 'path'
import { isExternal } from '@/utils/validate'
import Item from './Item'
import AppLink from './Link'
import FixiOSBug from './FixiOSBug'

export default {
  name: 'SidebarItem',
  components: { Item, AppLink },
  mixins: [FixiOSBug],
  props: {
    // route object
    item: {
      type: Object,
      required: true
    },
    isNest: {
      type: Boolean,
      default: false
    },
    basePath: {
      type: String,
      default: ''
    }
  },
  data () {
    this.onlyOneChild = null
    return {}
  },
  methods: {
    hasOneShowingChild (children = [], parent) {
      if (!children) {
        children = [];
      }
      const showingChildren = children.filter(item => {
        if (item.hidden) {
          return false
        } else {
          // Temp set(will be used if only has one showing child)
          this.onlyOneChild = item
          return true
        }
      })

      // When there is only one child router, the child router is displayed by default
      if (showingChildren.length === 1) {
        return true
      }

      // Show parent if there are no child router to display
      if (showingChildren.length === 0) {
        this.onlyOneChild = { ...parent, path: '', noShowingChildren: true }
        return true
      }

      return false
    },
    resolvePath (routePath, routeQuery) {
      if (isExternal(routePath)) {
        return routePath
      }
      if (isExternal(this.basePath)) {
        return this.basePath
      }
      if (routeQuery) {
        let query = JSON.parse(routeQuery);
        return { path: path.resolve(this.basePath, routePath), query: query }
      }
      return path.resolve(this.basePath, routePath)
    }
  }
}
</script>
<style lang="scss" scoped>
</style>
