<template>
  <menus-button
    ico="align-left"
    text="左对齐"
    shortcut="Ctrl+Shift+L"
    hide-text
    :menu-active="
      editor?.isActive({ textAlign: 'left' }) ||
      editor?.isActive({ nodeAlign: 'flex-start' })
    "
    :disabled="
      !editor?.can().chain().focus().setTextAlign('left').run() &&
      !editor?.can().chain().focus().setNodeAlign('left').run()
    "
    @menu-click="setAlignLeft"
  />
</template>

<script setup>
const { editor } = useStore()
const setAlignLeft = () => {
  if (editor.value?.can().chain().focus().setTextAlign('left').run()) {
    editor.value?.chain().focus().setTextAlign('left').run()
  }
  if (editor.value?.can().chain().focus().setNodeAlign('flex-start').run()) {
    editor.value?.chain().focus().setNodeAlign('flex-start').run()
  }
}
</script>
