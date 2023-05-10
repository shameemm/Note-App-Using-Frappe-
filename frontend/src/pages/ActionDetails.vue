<template>
    <div class="mx-20 my-4" v-if="!action.loading">
        <div class="flex flex-row items-center justify-between">
            <h1 class="font-black text-5xl text-gray-900">{{ action.doc.title }}</h1>
            <div class="flex flex-row item-center space-x-1">
                <Button icon-left="arrow-left" @click="router.back()">Go back</Button>
                <Button 
                @click="action.setValue.submit({ status:'Archived'})"
                v-if="action.doc.status != 'Archived'"
                appearance="white" class="text-red-600 border-red-600" icon-left="trash">Delete</Button>
                <Button appearance="white"
                @click="action.setValue.submit({ status:'Completed'})"
                v-if="action.doc.status!='Completed' || action.doc.status != 'Archived'"
                class="text-green-600 border-green-600" icon-left="check">Mark As Done</Button>
            </div>
            
        </div>

        <div>
            <!-- <TextEditor
            editor-class="prose-sm border max-w-none rounded-b-lg p-3 overflow-auto h-64 focus:outline-none"
            :fixedMenu="true"
            :content="content"
            @change="(val) => (content = val)"
            /> -->
        </div>
        
    </div>
    <LoadingIndicator v-else/>
    
    
</template>

<script setup>
import { TextEditor ,createDocumentResource,LoadingIndicator } from 'frappe-ui';
import { ref } from 'vue';
import { useRouter } from 'vue-router';
const router = useRouter()
const props = defineProps(['name'])

const content = ref('')

const action = createDocumentResource({
    doctype: 'Action',
    name: props.name,
    onSuccess(data) {
        console.log(data);
    }
})
action.reload( )
</script>


