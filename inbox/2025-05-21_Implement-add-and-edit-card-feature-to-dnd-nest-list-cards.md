---
date: 2025-05-21
type: fact
aliases:
  -
hubs:
  - "[[vue]]"
---

# Implement add and edit card feature to dnd nest list cards

```vue
<!--src/App.vue-->
<script setup lang="ts">
import {onMounted, onUnmounted, reactive, ref} from 'vue'
import Draggable from 'vuedraggable'
import ModalDialog from './components/ModalDialog.vue';
import type { List } from './types';

const lists = reactive<List[]>([
  {
    id: 1,
    title: 'To Do',
    cards: [
      { id: 1, title: 'Task 1', description: 'Description for Task 1' },
      { id: 2, title: 'Task 2', description: 'Description for Task 2' }
    ]
  },
  {
    id: 2,
    title: 'In Progress',
    cards: [
      { id: 3, title: 'Task 3', description: 'Description for Task 3' },
      { id: 4, title: 'Task 4', description: 'Description for Task 4' }
    ]
  },
  {
    id: 3,
    title: 'Done',
    cards: [{ id: 5, title: 'Task 5', description: 'Description for Task 5' }]
  }
])
const isModalOpen = ref(false)

const openModal = () => {
  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false
}

const handleEscapeKey = (event: KeyboardEvent) => {
  if (event.key === 'Escape' && isModalOpen.value) {
    closeModal()
  }
}

onMounted(() => {
  document.addEventListener('keydown', handleEscapeKey)
})

onUnmounted(() => {
  document.removeEventListener('keydown', handleEscapeKey)
})
</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto">
      <div class="bg-gray-100 p-3 rounded-lg min-w-[250px] flex flex-col" v-for="list in lists" :key="list.id">
        <h2 class="font-medium mb-2">{{ list.title }}</h2>
        
        <Draggable :list="list.cards" group="cards" item-key="id">
          <template #item="{element}">
            <div class="bg-white p-2 my-2 rounded shadow cursor-pointer">
              <span class="text-sm font-medium">{{ element.title }}</span>
              <p class="text-xs text-gray-400">
                {{ element.description }}
              </p>
            </div>
          </template>
        </Draggable>

        <button class="w-full bg-transparent rounded hover:bg-white text-gray-500 p-2 text-left mt-2 text-sm font-medium" @click="openModal">
          + Add Card
        </button>
      </div>
    </div>

    <ModalDialog :is-open="isModalOpen" @close="closeModal" />
  </main>
</template>
```


```vue
<!--src/components/ModalDialog.vue-->

<script lang="ts" setup>
import { nextTick, ref, watch } from 'vue'
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'

const props = defineProps<{
  isOpen: boolean
}>()
const emit = defineEmits<{
  (e: 'close'): void
}>()
const titleInput = ref<HTMLInputElement | null>(null)

watch(() => props.isOpen, async (isOpen) => {
  if (isOpen) {
    await nextTick()
    activate()
    titleInput.value?.focus()
  } else {
    deactivate()
  }
})

const modalElement = ref<HTMLDivElement | null>(null)
const { activate, deactivate } = useFocusTrap(modalElement)
</script>

<template>
  <div v-if="isOpen" 
  class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center" role="dialog" aria-modal="true" ref="modalElement"
  @click.self="emit('close')">
    <div class="bg-white p-5 rounded max-w-md w-full">
      <h2 class="text-xl font-bold mb-4">Add New Card</h2>
      <input type="text" placeholder="Card Title" aria-label="Card Title" class="w-full p-2 mb-4 border rounded" ref="titleInput" />
      <textarea class="w-full p-2 mb-4 border rounded" placeholder="Description" aria-label="Card Description"></textarea>

      <div class="flex justify-end gap-2">
        <button class="bg-gray-300 hover:bg-gray-200 text-black px-4 py-2 rounded" @click="emit('close')">Cancel</button>
        <button class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded" @click="emit('close')">Save</button>
      </div>
    </div>
  </div>
</template>
```

Above is a draggable list of cards structure that has implemented the toggle function of a modal dialog. Next, we want to implement the functionality of adding and editing cards in the modal dialog.


```vue
<!--src/App.vue-->
<script setup lang="ts">

// ...

// because we only use single modal dialog to add and edit different cards, we need to track the current card being edited and which list it belongs to
const editingCard = ref<Card | null>(null)
const editingListIndex = ref<number | null>(null)

// computing whether the modal is in add or edit mode from the editingCard,
// it will be convenient to use this value in the modal dialog to show different titles and button labels or anything else
const modalMode = computed(() => editingCard.value === null ? 'add' : 'edit')

const openModal = (listIndex: number, card?: Card) => {
//                 ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ // add parameters in order to keep track of the list and card being edited

  // reset the refs to the current editing card and list
  editingListIndex.value = listIndex
  editingCard.value = card === undefined ? null : card

  isModalOpen.value = true
}

const closeModal = () => {
  isModalOpen.value = false

  // reset the refs to null when closing the modal in order to avoid modal dialog showing the previous card
  editingListIndex.value = null
  editingCard.value = null
}

// we need a new function to save the card to the list
const saveCard = (card: Card) => {
//                ^^^^^^^^^^ this information should come from the modal dialog bye new event 'save', we will create this event in the modal dialog later
  if (editingListIndex.value === null) {
    return
  }

  // based on the modalMode, we do different logics to add or edit the card
  if (modalMode.value === 'add') {
    // Adding

    // get the new card id by getting the max id from all cards in all lists
    const newId = Math.max(
      ...lists.flatMap(list => list.cards.map(c => c.id))
    )
    // push the new card with the new id to the list
    lists[editingListIndex.value].cards.push(
      {...card, id: newId}
    )
  } else {
    // Modify

    // find the index of the editing card in the list
    const cardIndex = lists[editingListIndex.value]
      .cards.findIndex(
        (cardOnList) => cardOnList.id === card.id
      )

    // replace the card in the list with the new card
    if (cardIndex !== -1) {
      lists[editingListIndex.value].cards[cardIndex] = card
    }
  }

  closeModal()
}


</script>

<template>
  <main class="p-5 font-sans">
    <div class="flex gap-5 py-5 overflow-x-auto">
      <div class="bg-gray-100 p-3 rounded-lg min-w-[250px] flex flex-col" v-for="(list, listIndex) in lists" :key="list.id">
        <h2 class="font-medium mb-2">{{ list.title }}</h2>
        
        <Draggable :list="list.cards" group="cards" item-key="id">
          <template #item="{element}">
            <div @click="openModal(listIndex, element)" class="bg-white p-2 my-2 rounded shadow cursor-pointer">
            <!-- ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ now each card can be clicked to open the modal dialog with the card information (refresh the refs here) -->
              <span class="text-sm font-medium">{{ element.title }}</span>
              <p class="text-xs text-gray-400">
                {{ element.description }}
              </p>
            </div>
          </template>
        </Draggable>

        <button class="w-full bg-transparent rounded hover:bg-white text-gray-500 p-2 text-left mt-2 text-sm font-medium" @click="openModal(listIndex)">
        <!--                                                                                                                               ^^^^^^^^^^^ adding card only needs the list index -->
          + Add Card
        </button>
      </div>
    </div>

    <ModalDialog :is-open="isModalOpen" :card="editingCard" :mode="modalMode" @close="closeModal" @save="saveCard" />
    <!--                                ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^                     ^^^^^^^^^^^^^^^^ -->
    <!--                                ModalDialog add new props 'card' and 'mode' to pass the current card and mode to the modal dialog -->
    <!--                                and new event 'save' to trigger the save function and pass the new card or edited card to the function -->
  </main>
</template>
```

```vue
<!--src/components/ModalDialog.vue-->
<script lang="ts" setup>

// ...

const props = defineProps<{
  isOpen: boolean

  // new props
  card: Card | null
  mode: 'add' | 'edit'
}>()

const emit = defineEmits<{
  (e: 'close'): void
  (e: 'save', card: Card): void // new event to save the card
}>()

// ...

// new ref to store the card information locally, it should be initialized with the card prop if it is not null
const localCard = ref<Card>({
  id: 0, title: '', description: '' // default values if the card is null
})

// ...

// the better way to initialize the ref with props is to use watch
watch(() => props.card, (newCard) => {
  if (newCard) {
    localCard.value = {...newCard}
  } else {
    localCard.value = {id: 0, title: '', description: ''} // if the card is null, reset the localCard to default values
  }                                                       // this is also important to avoid the modal dialog showing the previous card when closing the modal
}, {immediate: true})
// ^^^^^^^^^^^^^^^^^ not only wait for the prop to change, but also run the function immediately when the component or props just mounted

// ...

</script>

<template>
  <div v-if="isOpen" @keydown.esc="emit('close')" 
  class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center" role="dialog" aria-modal="true" ref="modalElement" @click.self="emit('close')">
    <div class="bg-white p-5 rounded max-w-md w-full">
      <h2 class="text-xl font-bold mb-4">
        {{ mode === 'add' ? 'Add New Card': 'Edit Card' }} <!-- new title based on the mode -->
      </h2>
      <input v-model="localCard.title" type="text" placeholder="Card Title" aria-label="Card Title" class="w-full p-2 mb-4 border rounded" ref="titleInput" />
      <!--   ^^^^^^^^^^^^^^^^^^^^^^^^^ bind the input value to the localCard title property -->

      <textarea v-model="localCard.description" class="w-full p-2 mb-4 border rounded" placeholder="Description" aria-label="Card Description"></textarea>
      <!--      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ bind the textarea value to the localCard description property -->

      <div class="flex justify-end gap-2">
        <button class="bg-gray-300 hover:bg-gray-200 text-black px-4 py-2 rounded" @click="emit('close')">Cancel</button>

        <button class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-2 rounded" @click="emit('save', localCard)">{{ mode === 'add' ? 'Add' : 'Save' }}</button>
        <!--                                                                                   ^^^^^^^^^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^  -->
        <!--                                                                                   emit event 'save' with the localCard as the parameter to save the card -->
      </div>
    </div>
  </div>
</template>
```

## What we have learned

1. When we want to use the same component to handle different things, we need to add some refs to track which thing we are handling.
2. Use `watch` to initialize the refs with the props when the component is mounted. And better add `immediate: true` to the watch to run the function immediately when the component is mounted.
3. recovering the refs to default values is important to avoid the modal dialog showing the previous card when closing the modal.
