<script setup lang="ts">
type Data = {
  title: string
  children?: Data[]
}

const props = withDefaults(
  defineProps<{
    item: Data
    index: number
    onDbClick: () => void
  }>(),
  {
    index: 0,
  },
)

const itemType = props.item.children ? 'folder' : 'file'
const src = new URL(`../assets/${itemType}.svg`, import.meta.url).href
const alt = `${props.item.title} ${itemType}`
</script>

<template>
  <div class="item" :tabindex="index + 1" @dblclick="onDbClick">
    <p class="item__title">{{ item.title }}</p>
    <img :src="src" :alt="alt" width="100" height="100" />
  </div>
</template>

<style lang="scss">
.item {
  display: flex;

  flex-direction: column-reverse;

  gap: 10px;

  align-items: center;
  justify-content: flex-end;

  width: 120px;

  padding: 10px;

  border: 1px solid rgba(#000000, 0.1);
  border-radius: 5px;

  &:focus {
    background: rgba(#000000, 0.1);
  }

  &__title {
    width: 100%;
    overflow: hidden;

    font-size: 16px;

    text-align: center;

    text-overflow: ellipsis;

    white-space: nowrap;
  }
}
</style>
