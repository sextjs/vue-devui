<template>
  <div class="wrapper">
    <div id="menuLink" class="menu-link">
      <span></span>
    </div>
    <div class="devui-components-search">
      <!-- TODO: 搜索框 -->
    </div>
    <div class="sidebar">
      <ul class="devui-menu" style="margin-bottom: 0">
        <li class="devui-menu-item">
          <a href="/get-start">快速开始</a>
        </li>
        <li class="devui-menu-item">
          <a href="/color" style="position: relative"
            >颜色变量
            <sup class="devui-beta-label">Beta</sup>
          </a>
        </li>
        <li class="devui-menu-item">
          <a href="/theme-guide" style="position: relative">主题化使用指南 </a>
        </li>
      </ul>
      <nav class="side-nav">
        <!-- TODO: 左侧组件导航 d-accordion -->
        <d-accordion :data="componentsData" :linkType="'routerLink'"></d-accordion>
      </nav>
    </div>
    <div class="doc-viewer-container">
      <!-- TODO: 文档主体内容 -->
      <!-- <app-demo-cell></app-demo-cell> -->
      <router-view :key="key"></router-view>
    </div>
  </div>
</template>

<script lang="ts">
import { groupBy, map } from 'lodash-es'
import { routesConfig } from './component.route'
import AppDemoCell from './app-demo-cell.vue'
import DevUIAccordion from '../../devui/accordion/accordion.vue'

export default {
  name: 'app-content',
  components: {
    AppDemoCell,
    'd-accordion': DevUIAccordion
  },
  data(): any {
    return {
      componentsData: []
    }
  },
  computed: {
    key() {
      return this.$route.fullPath
    }
  },
  created() {
    this.generateComponentData();
  },
  methods: {
    generateComponentData() {
      // TODO: 补充类型
      const routesWithData = map(routesConfig, (route: any) => {
        if (!route.meta) {
          route.meta = {};
        }
        return route;
      });
      const groupedRoutesObj = groupBy(routesWithData,
        (route: any) => {
          return (route as any).meta.type || '通用';
        });
      for (const key in groupedRoutesObj) {
        if (key) {
          const group = groupedRoutesObj[key].map((item: any) => {
            if (item.meta.name) {
              return {
                title: item.meta.name + ' ' + item.meta.cnName,
                link: item.path,
              };
            } else {
              return {};
            }
          }
          ).filter((item: any) => Object.keys(item).length !== 0)
            .sort(function (s1: any, s2: any) {
              const prev = (s1.title).toUpperCase();
              const next = (s2.title).toUpperCase();
              if (prev < next) {
                return -1;
              }
              if (prev > next) {
                return 1;
              }
              return 0;
            });
          const componentItem: any = { title: key, children: group, open: true };
          this.componentsData.push(componentItem);
        }
      }
    }
  },
}
</script>

<style lang="scss">
  
</style>