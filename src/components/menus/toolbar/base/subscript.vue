<template>
  <menus-button
    ico="subscript"
    text="下标"
    shortcut="Ctrl+,"
    hide-text
    :menu-active="editor?.isActive('subscript')"
    :disabled="!editor?.can().chain().focus().toggleSubscript().run()"
    @menu-click="editor?.commands.toggleSubscript()"
  />
</template>

<script setup>
const { editor } = useStore()
</script>
