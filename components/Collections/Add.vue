<template>
  <div>
    <div>
      <label for="email" class="block text-sm font-medium leading-6 text-gray-900">ADD TASK</label>
      <div class="mt-2">
        <input v-model="taskName" type="text" name="email" id="email" class="block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 focus:ring-2 focus:ring-inset focus:ring-indigo-600 sm:text-sm sm:leading-6" />
      </div>
    </div>
    <div class="mt-5 sm:mt-6 sm:grid sm:grid-flow-row-dense sm:grid-cols-2 sm:gap-3">
      <button
        type="button"
        class="inline-flex w-full justify-center rounded-md bg-gray-600 px-3 py-2 text-sm font-semibold text-white shadow-sm hover:bg-indigo-500 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2 focus-visible:outline-indigo-600 sm:col-start-2"
        @click="saveTask(false)"
      >
        Save
      </button>

      <button type="button" class="mt-3 inline-flex w-full justify-center rounded-md bg-white px-3 py-2 text-sm font-semibold text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 hover:bg-gray-50 sm:col-start-1 sm:mt-0" @click="closeModal(false)">
        Cancel
      </button>
    </div>
  </div>
</template>
<script setup>
  const taskName = ref("");

  const emit = defineEmits(["save-task", "hide"]);
  // Close modal
  const closeModal = (close) => {
    emit("hide", close);
  };
  // Save task
  const saveTask = (close) => {
    emit("hide", close);
    emit("Addtask", taskName.value);
  };
</script>
