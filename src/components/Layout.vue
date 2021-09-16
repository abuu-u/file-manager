<script setup lang="ts">
import { ref } from 'vue'
import { onBeforeRouteUpdate, useRoute, useRouter } from 'vue-router'
import Item from './Item.vue'

type Data = {
  title: string
  children?: Data[]
}

type NavLink = {
  href: string
  title: string
}

const structure = ref<Data>({
  title: 'home',

  children: [
    {
      title: 'asd1111111111111111111111',
      children: [
        {
          title: 'asd11',
          children: [{ title: 'asd111' }, { title: 'asd112' }],
        },
        { title: 'asd12' },
        { title: 'asd13' },
      ],
    },
    {
      title: 'asd2',
      children: [
        {
          title: 'asd21',
          children: [{ title: 'asd211' }, { title: 'asd212' }],
        },
        { title: 'asd22' },
        { title: 'asd23' },
      ],
    },
    {
      title: 'asd3',
      children: [
        {
          title: 'asd31',
          children: [{ title: 'asd311' }, { title: 'asd312' }],
        },
        { title: 'asd32' },
        { title: 'asd33' },
      ],
    },
  ],
})

const route = useRoute()
const router = useRouter()
const paths = ref(route.params.path)
const currStructure = ref(structure.value)

const navLinks = ref<NavLink[]>([])

const updateData = () => {
  if (!Array.isArray(paths.value)) {
    return
  }

  const links = paths.value

  navLinks.value.push({ href: '/', title: structure.value.title })

  for (let i = 0; i < links.length; i += 1) {
    const link = links[i]
    const numberPath = parseInt(link, 10)
    const item = currStructure.value.children?.[parseInt(link, 10)]

    if (Number.isNaN(numberPath) || !item) {
      router.push('/')
      return
    }

    currStructure.value = item

    if (i < links.length - 1) {
      navLinks.value.push({
        href: `/${links.slice(0, i + 1).join('/')}`,
        title: item.title,
      })
    }
  }
}

onBeforeRouteUpdate((to) => {
  paths.value = to.params.path
  currStructure.value = structure.value
  navLinks.value = []

  updateData()
})

updateData()

const handleDbClick = (item: Data, index: number) => {
  if (!item.children) {
    return
  }

  const pushPath = paths.value === '' ? `${index}` : `/${paths.value}/${index}`

  router.push(pushPath)
}
</script>

<template>
  <ul class="breadcrumbs">
    <li v-for="link in navLinks" :key="link.href" class="breadcrumbs__item">
      <router-link class="breadcrumbs__link" :to="link.href">{{
        link.title
      }}</router-link>
    </li>
    <li class="breadcrumbs__item">
      <a class="breadcrumbs__link breadcrumbs__link--active">{{
        currStructure.title
      }}</a>
    </li>
  </ul>
  <div class="wrapper">
    <Item
      v-for="(item, i) in currStructure.children"
      :key="item.title"
      :index="i"
      :item="item"
      :on-db-click="() => handleDbClick(item, i)"
    />
  </div>
</template>

<style lang="scss">
.wrapper {
  display: flex;

  flex-wrap: wrap;

  gap: 20px;

  padding: 2% 4%;
}

.breadcrumbs {
  display: flex;

  padding: 0 4%;

  list-style: none;

  border-bottom: 1px solid #000000;

  &__item {
    position: relative;

    display: flex;

    align-items: center;

    font-size: 18px;

    &::before,
    &::after {
      position: absolute;
      left: 0;

      width: 3px;

      height: 12px;

      content: '';

      background: #000000;
      border-radius: 3px;
    }

    &::before {
      margin-top: 7px;

      transform: rotate(45deg);
    }

    &::after {
      margin-bottom: 7px;

      transform: rotate(-45deg);
    }
  }

  &__link {
    position: relative;

    display: flex;

    flex-direction: column;

    padding: 10px 0;
    margin: 0 12px 0 20px;

    color: rgb(0, 89, 255);

    &--active::after {
      position: absolute;
      bottom: 8px;

      width: 100%;
      height: 2px;

      content: '';

      background: rgb(0, 89, 255);
    }
  }
}
</style>
