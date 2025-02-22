<template>
  <node-view-wrapper
    ref="containerRef"
    class="node-view image-node-view"
    :style="{ 'justify-content': node.attrs.nodeAlign }"
    @dblclick="imagePreview = node.attrs.src"
  >
    <div class="node-container hover-shadow select-outline image">
      <div v-if="node.attrs.src && isLoading" class="loading">
        <icon name="loading" class="loading-icon" />
        加载中...
      </div>
      <div class="error" v-else-if="node.attrs.src && error">
        <icon name="image-failed" class="error-icon" />
        图片加载错误
      </div>
      <drager
        v-else
        :selected="selected"
        :rotatable="true"
        :boundary="false"
        :draggable="Boolean(node.attrs.draggable) && !options.document.readOnly"
        :angle="node.attrs.angle"
        :width="Number(node.attrs.width)"
        :height="Number(node.attrs.height)"
        :left="Number(node.attrs.left)"
        :top="Number(node.attrs.top)"
        :min-width="14"
        :min-height="14"
        :max-width="maxWidth"
        :z-index="10"
        :equal-proportion="node.attrs.equalProportion"
        @rotate="onRotate"
        @resize="onResize"
        @drag="onDrag"
        @click="selected = true"
      >
        <img
          ref="imageRef"
          :src="node.attrs.src"
          :style="{
            height: node.attrs.equalProportion ? 'auto' : '100%',
            transform:
              node.attrs.flipX || node.attrs.flipX
                ? `rotateX(${node.attrs.flipX ? '180' : '0'}deg) rotateY(${node.attrs.flipY ? '180' : '0'}deg)`
                : 'none',
          }"
          @load="onLoad"
        />
        <div
          v-if="!node.attrs.uploaded && node.attrs.file !== null"
          class="uploading"
        >
          <span></span>
        </div>
      </drager>
    </div>
  </node-view-wrapper>
</template>

<script setup>
import { nodeViewProps, NodeViewWrapper } from '@tiptap/vue-3'
import Drager from 'es-drager'
import { base64ToFile } from 'file64'
import generateId from '@/utils/generate-id'

const { node, updateAttributes } = defineProps(nodeViewProps)
const { options } = useStore()
const { imagePreview } = useStore()
const { isLoading, error } = useImage({ src: node.attrs.src })

const containerRef = ref(null)
const imageRef = $ref(null)
let selected = $ref(false)
let maxWidth = $ref(0)

const uploadImage = async () => {
  if (node.attrs.uploaded || !node.attrs.file) {
    return
  }
  try {
    const { id, url } = await options.value.onFileUpload(node.attrs.file)
    if (containerRef.value) {
      updateAttributes({ id, src: url, file: null, uploaded: true })
    }
  } catch (error) {
    useMessage('error', error.message)
  }
}
onMounted(async () => {
  const width = containerRef.value.$el.clientWidth
  maxWidth = width
  if (node.attrs.width === null) {
    updateAttributes({ width })
  }
})
const onLoad = () => {
  const height = imageRef?.offsetHeight
  if (containerRef.value && height) {
    updateAttributes({ height })
  }
}

const onRotate = ({ angle }) => {
  updateAttributes({ angle })
}
const onResize = ({ width, height }) => {
  updateAttributes({ width, height })
}
const onDrag = ({ left, top }) => {
  updateAttributes({ left, top })
}

onClickOutside(containerRef, () => (selected = false))

watch(
  () => node.attrs.equalProportion,
  async () => {
    await nextTick()
    const width = imageRef.offsetWidth
    const height = imageRef.offsetHeight
    updateAttributes({ width, height })
  },
)
watch(
  () => node.attrs.src,
  async (src) => {
    if (node.attrs.uploaded === false && !error.value) {
      if (src.startsWith('data:image')) {
        const type = src.split(';')[0].split(':')[1]
        let ext = type.split('/')[1]
        if (ext === 'jpeg') ext = 'jpg'
        if (ext === 'svg+xml') ext = 'svg'
        const filename = generateId(10)
        const file = await base64ToFile(src, `${filename}.${ext}`, {
          type,
        })
        updateAttributes({ file })
      }
      await nextTick()
      uploadImage()
    }
  },
  { immediate: true },
)
watch(
  () => error.value,
  ({ type }) => {
    updateAttributes({ error: type === 'error' })
  },
)
</script>

<style lang="less">
.node-view {
  .image {
    max-width: 100%;
    width: auto;
    display: inline-flex;
    img {
      display: block;
      width: 100%;
    }
    .loading {
      width: 160px;
      height: 120px;
      display: flex;
      align-items: center;
      justify-content: center;
      color: var(--umo-text-color-light);
      background: rgba(0, 0, 0, 0.02);
      font-size: 12px;
      gap: 10px;
      .loading-icon {
        color: var(--umo-primary-color);
        font-size: 22px;
        animation: turn 1s linear infinite;
      }
    }
    .error {
      width: 160px;
      height: 120px;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      color: var(--umo-text-color-light);
      background: rgba(0, 0, 0, 0.02);
      font-size: 12px;
      .error-icon {
        font-size: 72px;
        margin: -8px 0 2px;
      }
    }
    .uploading {
      position: absolute;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      background-color: rgba(0, 0, 0, 0.1);
      span {
        display: block;
        position: absolute;
        background: rgba(0, 0, 0, 0.2);
        height: 4px;
        border-radius: 2px;
        top: 50%;
        left: 20%;
        right: 20%;
        transform: translateY(-50%);
        overflow: hidden;
        &:after {
          content: '';
          display: block;
          height: 100%;
          background-color: var(--umo-primary-color);
          animation: progress 1s linear infinite;
        }
      }
    }
  }
}

@keyframes turn {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
@keyframes progress {
  0% {
    width: 0;
  }
  100% {
    width: 100%;
  }
}
</style>
