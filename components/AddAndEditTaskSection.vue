<template>
	<div
		class="w-full h-full px-6 py-6 bg-white md:h-fit md:border md:px-10 md:py-8 md:rounded-lg xl:w-2/6 md:w-3/5"
	>
		<div class="flex justify-between">
			<p class="mb-3 text-2xl font-bold">
				{{ isEditTaskMode ? 'Edit Task' : 'Add Task' }}
			</p>
			<button
				@click="$emit('backward')"
				type="button"
				class="px-5 py-2 text-sm font-medium text-gray-900 bg-white border border-gray-400 rounded-lg hover:bg-gray-100"
			>
				Backward
			</button>
		</div>
		<form class="flex flex-col gap-4">
			<div>
				<div class="flex gap-1">
					<label
						for="title"
						class="block text-sm font-medium leading-6 text-gray-900"
						>Title</label
					>
					<span class="text-sm font-medium text-red-500"> * </span>
					<span class="text-sm font-medium text-red-500">{{
						errMessage.title
					}}</span>
				</div>

				<div class="mt-1">
					<input
						type="text"
						name="title"
						id="title"
						class="px-2 block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-gray-300 placeholder:text-gray-400 disabled:cursor-not-allowed disabled:bg-gray-50 disabled:text-gray-500 disabled:ring-gray-200 sm:text-sm sm:leading-6 focus:outline-none focus:ring-gray-400"
						placeholder="Type task title here"
						v-model="taskForm.title"
					/>
				</div>
			</div>
			<div>
				<div class="flex gap-1">
					<label
						for="description"
						class="block text-sm font-medium leading-6 text-gray-900"
						>Description</label
					>
					<span class="text-sm font-medium text-red-500"> * </span>
					<span class="text-sm font-medium text-red-500">{{
						errMessage.description
					}}</span>
				</div>
				<div class="mt-1">
					<textarea
						placeholder="Type task description here"
						rows="4"
						name="description"
						id="description"
						class="px-2 block w-full rounded-md border-0 py-1.5 text-gray-900 shadow-sm ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 sm:text-sm sm:leading-6 focus:outline-none focus:ring-gray-400"
						v-model="taskForm.description"
					/>
				</div>
			</div>
			<div>
				<div class="flex gap-1">
					<label class="block text-sm font-medium leading-6 text-gray-900"
						>Due date</label
					>
					<span class="text-sm font-medium text-red-500"> * </span>
					<span class="text-sm font-medium text-red-500">{{
						errMessage.dueDate
					}}</span>
				</div>
				<VueDatePicker
					:min-date="new Date()"
					v-model="taskForm.dueDate"
					:enable-time-picker="false"
					placeholder="Select Date"
				></VueDatePicker>
			</div>
			<div class="flex items-center mt-2">
				<button
					v-if="isEditTaskMode === true"
					@click="submitForm"
					type="button"
					class="w-full px-5 py-2 text-sm font-medium text-white bg-blue-700 rounded-lg disabled:bg-gray-300 disabled:cursor-default hover:bg-blue-800"
				>
					Edit Task
				</button>
				<button
					v-else
					@click="submitForm"
					type="button"
					class="w-full px-5 py-2 text-sm font-medium text-white bg-blue-700 rounded-lg disabled:bg-gray-300 disabled:cursor-default hover:bg-blue-800"
				>
					Add Task
				</button>
			</div>
		</form>
	</div>
</template>
<script setup>
import VueDatePicker from '@vuepic/vue-datepicker'
import '@vuepic/vue-datepicker/dist/main.css'
const props = defineProps({
	isEditTaskMode: {
		type: Boolean,
		default: false
	},
	task: {
		type: Object,
		require: true
	}
})

const emit = defineEmits(['backward', 'addTask', 'editTask'])

const taskForm = ref({
	title: props.task.title,
	description: props.task.description,
	dueDate: props.task.dueDate,
	isCompletedTask: props.task.isCompletedTask
})

const errMessage = ref({
	title: '',
	description: '',
	dueDate: ''
})

const submitForm = () => {
	let valid = true
	if (!taskForm.value.title) {
		errMessage.value.title = 'Title is required'
		valid = false
	} else {
		errMessage.value.title = ''
	}

	if (!taskForm.value.description) {
		errMessage.value.description = 'Description is required'
		valid = false
	} else {
		errMessage.value.description = ''
	}

	if (!taskForm.value.dueDate) {
		errMessage.value.dueDate = 'Due date is required'
		valid = false
	} else {
		errMessage.value.dueDate = ''
	}

	if (valid) {
		if (props.isEditTaskMode === true) {
			emit('editTask', taskForm.value)
		} else {
			taskForm.value.isCompletedTask = false
			emit('addTask', taskForm.value)
		}
	}
}
</script>

<style scoped></style>
