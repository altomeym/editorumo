<template>
  <menus-button
    text="增加缩进"
    ico="indent"
    shortcut="Tab"
    hide-text
    @menu-click="editor?.chain().focus().indent().run()"
  />
</template>

<script setup>
const { editor } = useStore()
</script>
