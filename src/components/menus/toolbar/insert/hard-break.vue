<template>
  <menus-button
    ico="hard-break"
    text="换行"
    huge
    shortcut="Shift+Enter"
    @menu-click="editor?.chain().focus().setHardBreak().run()"
  />
</template>

<script setup>
const { editor } = useStore()
</script>
