<template>
  <menus-button
    ico="bullet-list"
    text="项目符号"
    shortcut="Ctrl+Shift+8"
    menu-type="popup"
    popup-handle="arrow"
    hide-text
    :menu-active="editor?.isActive('bulletList')"
    :popup-visible="popupVisible"
    @toggle-popup="togglePopup"
    @menu-click="toggleBulletList(options[0].value)"
  >
    <template #content>
      <div class="bullet-list-group">
        <tooltip
          v-for="item in options"
          :key="item.value"
          :content="item.label"
        >
          <div
            class="bullet-list-item"
            :class="{ active: listType == item.value }"
            @click="toggleBulletList(item.value)"
          >
            <icon
              class="icon-bullet-list"
              :name="`bullet-list-${item.value}`"
            />
          </div>
        </tooltip>
      </div>
    </template>
  </menus-button>
</template>

<script setup>
let { popupVisible, togglePopup } = usePopup()
const { editor } = useStore()

const options = [
  { label: '圆点', value: 'disc' },
  { label: '空心圆点', value: 'circle' },
  { label: '方块', value: 'square' },
]

let listType = $ref('')
watch(
  () => popupVisible.value,
  (val) => {
    if (val && editor.value) {
      const { listStyleType } = editor.value.getAttributes('bulletList')
      listType = listStyleType?.listStyleType || listStyleType || ''
    }
  },
)
const toggleBulletList = (listStyleType) => {
  const chain = editor.value?.chain().focus()
  if (!editor.value?.isActive('bulletList')) {
    chain
      .toggleBulletList()
      .updateAttributes('bulletList', { listStyleType })
      .run()
  }
  // 切换列表类型
  else if (
    editor.value.getAttributes('bulletList').listStyleType !== listStyleType
  ) {
    chain.updateAttributes('bulletList', { listStyleType }).run()
  }
  // 关闭列表类型
  else {
    chain.toggleBulletList().run()
  }
  listType = listStyleType
  popupVisible.value = false
}
</script>

<style lang="less" scoped>
.bullet-list-group {
  display: flex;
  align-items: center;
  gap: 8px;
  .bullet-list-item {
    cursor: pointer;
    padding: 5px;
    border: solid 1px var(--umo-border-color);
    box-sizing: border-box;
    &:last-child {
      margin-right: 0;
    }
    &:hover {
      background-color: var(--umo-button-hover-background);
    }
    &.active {
      border-color: var(--umo-primary-color);
    }
  }
  .icon-bullet-list {
    font-size: 44px;
  }
}
</style>
