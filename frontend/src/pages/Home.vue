<template>
  <div class="mx-20">
    <!-- <ul>
    <li v-for="action in actions.data" :key="action.title">
      {{ action.title }} - {{ action.status }}
    </li>
   </ul> -->
    <div class="flex flex-row items-center justify-between">
      <h2 class="text-5xl font-black text-gray-900">Lists</h2>
      <Button icon-left="plus">New List </Button>
    </div>
    <div class="mt-2">
      <Card title="General">
        <div>
          <ul>
            <li class="flex flex-row space-y-2 item-center justify-between" v-for="action in actions.data"
              :key="action.title">
              <router-link :to="`/actions/${action.name}`">{{ action.title }}</router-link>
              <Button @click="completeAction(action.name)" icon="check" />
            </li>
          </ul>
          <Button @click="addActionDialogShown = true" icon-left="plus">New Action</Button>
        </div>
      </Card>
    </div>
    <Dialog :options="{
      title: 'Add New Action',
      actions: [
        {
          label: 'Add Action',
          appearance: 'primary',
          handler: ({ close }) => {
            addAction()
            close()
          },
        },
        { label: 'Cancel' },
      ]
    }" v-model="addActionDialogShown">
      <template #body-content>
        <div class="space-y-2">
          <Input
          v-model="action.title"
          type="text"
           required
          placeholder="Enter action title..." 
          lable="Title" />
          <Input v-model="action.category" type="select" label="List" :options="categoryOptions"/>
        </div>
      </template>
    </Dialog>
  </div>
</template>

<script  setup>
import { transform } from '@vue/compiler-core';
import { Dialog, createListResource, Card, Input } from 'frappe-ui'
import { computed, reactive, ref } from 'vue';

const action = reactive({
  title: '',
  category: 'General'
})

const addActionDialogShown = ref(false)

const actions = createListResource({
  doctype: 'Action',
  fields: ["name", "title", "status", "description", "date", "due_date"],
  filters: {
    status: 'ToDo',
  },
  limit: 100
})
actions.reload()

const categories = createListResource({
  doctype:'Category',
  fields: ['name','title'],
  transform(categories){
    return categories.map((c)=>({label:c.title,value:c.name}))

  }
})
categories.reload()
const categoryOptions = computed(() =>{
  if(categories.list.loading || !categories.data){
    return []
  }
  console.log(categories.data);
  return categories.data
})
const completeAction = (actionName) => {

  actions.setValue.submit({
    name: actionName,
    status: 'Completed',
    onSuccess() {
      actions.reload()
    }
    
  })
  actions.reload()
}

const addAction = () => {
  console.log(action);
  actions.insert.submit(action)
}
</script>
