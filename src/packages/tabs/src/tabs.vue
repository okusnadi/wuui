<template lang="pug">
  .wu-tabs(:class="tabclasses")
    .wu-tabs-bar
      .wu-tabs-nav-container
        .wu-tabs-nav-wrap
          .wu-tabs-nav-scroll
            .wu-tabs-nav(ref="nav")
              .wu-tabs-ink-bar(:class="inkClass", :style="inkStyle")
              .wu-tabs-tab(:class="navClass(nav)", v-for="nav in panes", @click="handleTabClick(nav,nav.name)") {{nav.tab}}
    .wu-tabs-content(:class="contentClasses")
      slot
</template>

<script>
  const prefixCls = 'wu-tabs'
  export default {
    name: 'WuTabs',

    componentName: 'WuTabs',

    props: {
      type: {
        type: String,
        default: 'line'
      },
      size: {
        type: String,
        default: 'default'
      },
      animated: {
        type: Boolean,
        default: true
      },
      value: [String, Number]
    },

    data () {
      return {
        prefixCls: prefixCls,
        activeKey: this.value,
        panes: [],
        barWidth: 0,
        barOffset: 0
      }
    },

    computed: {
      tabclasses () {
        return {
          [`${prefixCls}-line`]: this.type === 'line',
          [`${prefixCls}-no-animation`]: this.type !== 'line' && !this.animated,
          [`${prefixCls}-card`]: this.type === 'card',
          [`${prefixCls}-mini`]: this.size === 'small'
        }
      },
      inkClass () {
        return {
          [`${prefixCls}-ink-bar-animated`]: !this.animated
        }
      },
      inkStyle () {
        let style = {
          display: 'none',
          width: `${this.barWidth}px`
        }
        if (this.type === 'line') style.display = 'block'
        if (this.animated) {
          style.transform = `translate3d(${this.barOffset}px, 0px, 0px)`
        } else {
          style.left = `${this.barOffset}px`
        }

        return style
      },
      contentClasses () {
        return {
          [`${prefixCls}-content-animated`]: this.animated
        }
      }
    },

    watch: {
      value (newVal) {
        this.activeKey = newVal
      },
      activeKey () {
        this.updateInk()
      }
    },

    methods: {
      navClass (item) {
        return {
          [`${prefixCls}-tab-disabled`]: item.disabled,
          [`${prefixCls}-tab-active`]: item.name === this.activeKey
        }
      },
      addPanes (item) {
        this.panes.push(item)
      },

      handleTabClick (tab, navName) {
        if (tab.disabled) return
        this.setActiveKey(navName)
        this.$emit('on-click', navName)
      },

      setActiveKey (name) {
        this.activeKey = name
        this.$emit('input', name)
      },

      updateInk () {
        this.$nextTick(() => {
          const index = this.panes.findIndex((nav) => nav.name === this.activeKey)
          const prevTabs = this.$refs.nav.querySelectorAll(`.${prefixCls}-tab`)
          const tab = prevTabs[index]
          this.barWidth = parseFloat(tab.offsetWidth)
          if (index > 0) {
            let offset = 0
            const gutter = this.size === 'small' ? 0 : 24
            for (let i = 0; i < index; i++) {
              offset += parseFloat(prevTabs[i].offsetWidth) + gutter
            }
            this.barOffset = offset
          } else {
            this.barOffset = 0
          }
        })
      }

    },

    mounted () {
      this.updateInk()
    }
  }
</script>
