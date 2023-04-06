<template>
  <div class="p-4">
    <!-- List tasks -->

    <div>
      <div class="space-y-8">
        <button
          @click="openModel"
          type="button"
          class="rounded-md bg-gray-600 px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600"
        >
          ADD TASK
        </button>
      </div>
      <div v-if="!tasks.length">
        <p class="h3 mt-3">No task to show, Please click on add task</p>
      </div>
      <div class="mt-5">
        <div>
          <p class="h3">LIST OF TASKS</p>
        </div>
        <Collections-List :tasks="tasks" @edit="edit" @delete="deleteTask"></Collections-List>
      </div>
      <TransitionRoot as="template" :show="open">
        <Dialog as="div" class="relative z-10" @close="open = false">
          <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100" leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0">
            <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />
          </TransitionChild>

          <div class="fixed inset-0 z-10 overflow-y-auto">
            <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
              <TransitionChild
                as="template"
                enter="ease-out duration-300"
                enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                enter-to="opacity-100 translate-y-0 sm:scale-100"
                leave="ease-in duration-200"
                leave-from="opacity-100 translate-y-0 sm:scale-100"
                leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
              >
                <DialogPanel class="relative transform overflow-hidden rounded-lg bg-white px-4 pb-4 pt-5 text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg sm:p-6">
                  <Collections-Add @hide="closeModel" @Addtask="Addtask"></Collections-Add>
                </DialogPanel>
              </TransitionChild>
            </div>
          </div>
        </Dialog>
      </TransitionRoot>

      <!-- Edit Modal -->
      <TransitionRoot as="template" :show="editModel">
        <Dialog as="div" class="relative z-10" @close="editModel = false">
          <TransitionChild as="template" enter="ease-out duration-300" enter-from="opacity-0" enter-to="opacity-100" leave="ease-in duration-200" leave-from="opacity-100" leave-to="opacity-0">
            <div class="fixed inset-0 bg-gray-500 bg-opacity-75 transition-opacity" />
          </TransitionChild>

          <div class="fixed inset-0 z-10 overflow-y-auto">
            <div class="flex min-h-full items-end justify-center p-4 text-center sm:items-center sm:p-0">
              <TransitionChild
                as="template"
                enter="ease-out duration-300"
                enter-from="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
                enter-to="opacity-100 translate-y-0 sm:scale-100"
                leave="ease-in duration-200"
                leave-from="opacity-100 translate-y-0 sm:scale-100"
                leave-to="opacity-0 translate-y-4 sm:translate-y-0 sm:scale-95"
              >
                <DialogPanel class="relative transform overflow-hidden rounded-lg bg-white px-4 pb-4 pt-5 text-left shadow-xl transition-all sm:my-8 sm:w-full sm:max-w-lg sm:p-6">
                  <CollectionsEdit v-if="editModel" :editData="editData" :editModel="editModel" @hide="closeModel" @update="updateTask"></CollectionsEdit>
                </DialogPanel>
              </TransitionChild>
            </div>
          </div>
        </Dialog>
      </TransitionRoot>
    </div>
  </div>
</template>

<script setup lang="ts">
  import { ref } from "vue";
  import { Menu, MenuButton, MenuItem, MenuItems, Dialog, DialogPanel, DialogTitle, TransitionChild, TransitionRoot } from "@headlessui/vue";
  import { CodeBracketIcon, EllipsisVerticalIcon, FlagIcon, StarIcon } from "@heroicons/vue/20/solid";
 
  const props = withDefaults(defineProps<URLSchema>(), {
    project_id: "1",
    entityId: "2",
    entity: "CONTACTS",
    ownerID: "1",
    category: "COMPLETED",
    status: "NEW",
    description: "Completed Tasks",
    isActive: "ACTIVE",
    due_date: "2023-04-05",
  });

  interface URLSchema {
    project_id: String;
    entityId: String;
    entity: String;
    url: String;
    ownerID: string;
    category: string;
    status: String;
    description: String;
    isActive: string;
    due_date: String;
    getUrl: string;
  }
  const open = ref(false);

  let editModel = ref(false);
  const editData = ref({});
  const url = "https://v1-orm-lib.mars.hipso.cc/tasks";
  const getUrl = "https://v1-orm-lib.mars.hipso.cc/tasks/CONTACTS/2?offset=0&limit=100&sort_column=id&sort_direction=desc";
  const { pending, data: tasks } = useAuthLazyFetch(getUrl, {});
  const openModel = () => {
    open.value = true;
  };
  // Close modal
  const closeModel = (empty: any) => {
    editModel.value = false;
    open.value = false;
    open.value = empty;
  };
  // Save tasks
  const Addtask = async (task: string) => {
    const { data } = await useAuthLazyFetchPost(`${url}/${props.entity}/${props.entityId}`, {
      body: {
        project_id: props.project_id,
        name: task,
        entity: props.entity,
        entity_id: props.entityId,
        owner_id: props.ownerID,
        category: props.category,
        status: props.status,
        description: props.description,
        is_active: props.isActive,
        due_date: props.due_date,
        kanban_order: [],
      },
    });
    tasks.value.push(data.value);
  };
  // Edit Task
  const edit = (data: object) => {
    editModel.value = true;
    open.value = false;
    editData.value = data;
  };
  // Update tasks
  const updateTask = async (editedItem: any, name: string) => {
    useAuthLazyFetchPut(`${url}/${editedItem.uid}`, {
      body: {
        project_id: props.project_id,
        name: name,
        entity: props.entity,
        entity_id: props.entityId,
        owner_id: props.ownerID,
        category: props.category,
        status: props.status,
        description: props.description,
        is_active: props.isActive,
        due_date: props.due_date,
        kanban_order: [],
      },
    });
    let taskIndex = tasks.value.findIndex((data: any) => data.uid === editedItem.uid);
    if (taskIndex != -1) tasks.value[taskIndex].name = name;
  };
  // Delete tasks
  const deleteTask = (uid: any) => {
    useAuthLazyFetchDelete(`${url}/${uid}`, {});
    let taskIndex = tasks.value.findIndex((data: any) => data.uid === uid);
    if (taskIndex != -1) tasks.value.splice(taskIndex, 1);
  };
</script>
