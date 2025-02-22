<template>
  <toolbar-scrollable ref="scrollableRef" class="scrollable-container">
    <div class="classic-menu">
      <div v-if="menus.length > 1" class="virtual-group">
        <t-select
          v-model="currentMenu"
          :popup-props="{
            destroyOnClose: true,
            attach: container,
          }"
          size="small"
          auto-width
          borderless
          @change="toggoleMenu"
        >
          <template #prefixIcon>
            <icon name="menu" />
          </template>
          <t-option
            v-for="item in menus"
            :key="item.value"
            :label="item.label"
            :value="item.value"
          />
        </t-select>
      </div>
      <template v-if="currentMenu === 'base'">
        <div class="virtual-group">
          <menus-toolbar-base-undo />
          <menus-toolbar-base-redo />
          <menus-toolbar-base-select-all />
          <menus-toolbar-base-clear-format />
        </div>
        <div class="virtual-group">
          <menus-toolbar-base-heading />
          <menus-toolbar-base-font-family borderless />
          <menus-toolbar-base-font-size borderless />
          <menus-toolbar-base-bold />
          <menus-toolbar-base-italic />
          <menus-toolbar-base-underline />
          <menus-toolbar-base-strike />
          <menus-toolbar-base-subscript />
          <menus-toolbar-base-superscript />
          <menus-toolbar-base-color />
          <menus-toolbar-base-background-color />
          <menus-toolbar-base-highlight />
        </div>
        <div class="virtual-group">
          <menus-toolbar-base-ordered-list />
          <menus-toolbar-base-bullet-list />
          <menus-toolbar-base-task-list />
          <menus-toolbar-base-outdent />
          <menus-toolbar-base-indent />
          <menus-toolbar-base-line-height />
          <menus-toolbar-base-align-dropdown />
          <menus-toolbar-base-quote />
          <menus-toolbar-base-code v-if="!disableItem('code')" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-base-markdown />
          <menus-toolbar-base-search-replace />
        </div>
        <div class="virtual-group">
          <menus-toolbar-base-print v-if="!disableItem('print')" />
        </div>
      </template>
      <template v-if="currentMenu === 'insert'">
        <div class="virtual-group">
          <menus-toolbar-insert-link />
          <menus-toolbar-insert-image />
          <menus-toolbar-insert-video v-if="!disableItem('video')" />
          <menus-toolbar-insert-audio v-if="!disableItem('audio')" />
          <menus-toolbar-insert-file v-if="!disableItem('file')" />
          <menus-toolbar-insert-code-block v-if="!disableItem('code')" />
          <menus-toolbar-insert-special-characters />
          <menus-toolbar-insert-date />
          <menus-toolbar-insert-emoji v-if="!disableItem('emoji')" />
          <menus-toolbar-insert-mathematics
            v-if="!disableItem('mathematics')"
          />
        </div>
        <div class="virtual-group">
          <menus-toolbar-insert-hard-break />
          <menus-toolbar-insert-horizontal-line />
          <menus-toolbar-insert-toc />
          <menus-toolbar-insert-text-box />
        </div>
        <div class="virtual-group">
          <menus-toolbar-insert-template />
          <menus-toolbar-insert-web-page />
        </div>
      </template>
      <template v-if="currentMenu === 'table'">
        <div class="virtual-group">
          <menus-toolbar-table-insert />
          <menus-toolbar-table-fix />
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-cells-align />
          <menus-toolbar-table-cells-background />
          <!-- <menus-toolbar-table-border-color /> -->
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-add-row-before :huge="false" />
          <menus-toolbar-table-add-row-after :huge="false" />
          <menus-toolbar-table-add-column-before :huge="false" />
          <menus-toolbar-table-add-column-after :huge="false" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-delete-row :huge="false" />
          <menus-toolbar-table-delete-column :huge="false" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-merge-cells :huge="false" />
          <menus-toolbar-table-split-cell :huge="false" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-toggle-header-row :huge="false" />
          <menus-toolbar-table-toggle-header-column :huge="false" />
          <menus-toolbar-table-toggle-header-cell :huge="false" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-next-cell :huge="false" />
          <menus-toolbar-table-previous-cell :huge="false" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-table-delete />
        </div>
      </template>
      <template v-if="currentMenu === 'tools'">
        <div class="virtual-group">
          <menus-toolbar-tools-qrcode />
          <menus-toolbar-tools-barcode />
        </div>
        <div class="virtual-group">
          <menus-toolbar-tools-signature v-if="!disableItem('signature')" />
          <menus-toolbar-tools-seal v-if="!disableItem('seal')" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-tools-diagrams v-if="!disableItem('diagrams')" />
          <!-- <menus-toolbar-tools-mind-map v-if="!disableItem('mind-map')" /> -->
          <menus-toolbar-tools-mermaid v-if="!disableItem('mermaid')" />
        </div>
        <div class="virtual-group">
          <menus-toolbar-tools-chinese-case />
        </div>
      </template>
      <template v-if="currentMenu === 'page'">
        <div class="virtual-group">
          <menus-toolbar-page-toggle-toc />
        </div>
        <div class="virtual-group">
          <div class="virtual-group-row">
            <menus-toolbar-page-margin />
            <menus-toolbar-page-size />
            <menus-toolbar-page-orientation />
          </div>
        </div>
        <!-- <div class="virtual-group">
          <menus-toolbar-page-header />
          <menus-toolbar-page-footer />
        </div> -->
        <div class="virtual-group">
          <menus-toolbar-page-break />
          <menus-toolbar-page-break-marks />
          <menus-toolbar-page-line-number />
          <menus-toolbar-page-watermark />
          <menus-toolbar-page-background />
        </div>
        <div class="virtual-group">
          <menus-toolbar-page-preview />
        </div>
      </template>
      <template v-if="currentMenu === 'export'">
        <div class="virtual-group">
          <menus-toolbar-export-image />
          <!-- <menus-toolbar-export-pdf /> -->
          <menus-toolbar-export-html />
          <menus-toolbar-export-text />
        </div>
        <div class="virtual-group">
          <menus-toolbar-export-share v-if="!disableItem('share')" />
          <menus-toolbar-export-embed v-if="!disableItem('embed')" />
        </div>
      </template>
    </div>
  </toolbar-scrollable>
</template>

<script setup>
const props = defineProps({
  menus: {
    type: Array,
    required: true,
  },
  currentMenu: {
    type: String,
    required: true,
  },
})
const emits = defineEmits(['menu-change'])

const { container, options } = useStore()
const disableItem = (name) => {
  return options.value.toolbar.disableMenuItems.includes(name)
}

// eslint-disable-next-line vue/no-dupe-keys
let currentMenu = $ref('')
watch(
  () => props.currentMenu,
  async (val) => {
    currentMenu = val
    await nextTick()
    try {
      scrollableRef.update()
    } catch {}
  },
  { immediate: true },
)
const scrollableRef = $ref()
const toggoleMenu = async (menu) => {
  emits('menu-change', menu)
  await nextTick()
  scrollableRef.update()
}
</script>

<style lang="less" scoped>
.scrollable-container {
  padding: 10px;
}
.classic-menu {
  display: flex;
  align-items: center;
  flex: 1;
  .virtual-group {
    display: flex;
    align-items: center;
    &:empty {
      display: none;
    }
    &:not(:last-child)::after {
      content: '';
      display: block;
      height: 18px;
      width: 1px;
      background-color: var(--umo-border-color-light);
      margin: 0 10px;
    }
    :deep(.menu-button .umo-button--shape-square) {
      .icon {
        font-size: 14px;
      }
    }
    &-row {
      display: flex;
    }
  }
}
</style>
