<template>
	<div
		class="flex items-center justify-center h-screen m-auto md:py-8 bg-slate-100"
	>
		<!-- todo_wrapper -->
		<div
			class="px-6 py-6 bg-white border md:px-10 md:py-8 md:rounded-lg xl:w-1/2"
			v-if="currentTodoState === 'viewTask'"
		>
			<p class="mb-2 text-2xl font-bold xl:mb-3">To-Do List</p>
			<div class="flex justify-between">
				<div class="flex flex-col">
					<span>My Tasks</span>
					<span class="text-sm text-gray-500"
						>You have {{ countActiveTask }} tasks left!</span
					>
				</div>
				<div class="flex items-center">
					<button
						@click="goToAddTaskState"
						type="button"
						class="px-5 py-2 text-sm font-medium text-white bg-blue-700 rounded-lg hover:bg-blue-800"
					>
						Add Task
					</button>
				</div>
			</div>
			<div class="flex flex-col gap-4 lg:p-4 mt-5 overflow-y-auto max-h-[500px]">
				<TaskCard
					v-for="(task, index) in toDoList"
					:key="task.id"
					:task="task"
					@editTask="goToEditTask(index)"
					@deleteTask="deleteTask(index)"
					@checked="checkedTask(index, $event)"
				/>
			</div>
		</div>
		<!-- todo_wrapper_end -->
		<!-- create_todo -->
		<AddAndEditTaskSection
			:isEditTaskMode="isEditTaskMode"
			v-if="currentTodoState === 'addTask'"
			@backward="goToViewTaskState"
			@addTask="addTask"
			:task="selectedTask"
		/>
		<!-- create_todo_end -->
		<!-- edit_todo -->
		<AddAndEditTaskSection
			:isEditTaskMode="isEditTaskMode"
			v-else-if="currentTodoState === 'editTask'"
			@backward="goToViewTaskState"
			:task="selectedTask"
			@editTask="editTask"
		/>
		<!-- edit_todo_end -->
	</div>
</template>
<script setup>
import Swal from 'sweetalert2'
const toDoList = ref([
	{
		id: 1,
		title: 'Task 1',
		description:
			'loremLorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque vel ipsum neque. Quisque nec nibh egestas, accumsan erat a, suscipit lacus. In ac commodo odio. ',
		createAt: '2024-06-12T11:57:55.799Z',
		dueDate: '2024-06-14T11:57:55.799Z',
		isCompletedTask: false
	},
	{
		id: 2,
		title: 'Task 2',
		description:
			'loremLorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque vel ipsum neque. Quisque nec nibh egestas, accumsan erat a, suscipit lacus. In ac commodo odio. ',
		createAt: '2024-06-12T11:57:55.799Z',
		dueDate: '2024-06-14T11:57:55.799Z',
		isCompletedTask: false
	},
	{
		id: 3,
		title: 'Task 3',
		description:
			'loremLorem ipsum dolor sit amet, consectetur adipiscing elit. Pellentesque vel ipsum neque. Quisque nec nibh egestas, accumsan erat a, suscipit lacus. In ac commodo odio. ',
		createAt: '2024-06-12T11:57:55.799Z',
		dueDate: '2024-06-14T11:57:55.799Z',
		isCompletedTask: true
	}
])

const countActiveTask = computed(
	() => toDoList.value.filter((task) => task.isCompletedTask === false).length
)

const currentTodoState = ref('viewTask')

const goToAddTaskState = () => {
	currentTodoState.value = 'addTask'
}

const goToViewTaskState = () => {
	currentTodoState.value = 'viewTask'
	isEditTaskMode.value = false
	currentTaskIndex.value = null
	selectedTask.value = {
		title: '',
		description: '',
		dueDate: ''
	}
}

const addTask = (taskForm) => {
	taskForm.dueDate = new Date(taskForm.dueDate)
	toDoList.value.push({
		...taskForm,
		id: toDoList.value.length + 1,
		createAt: new Date().toISOString()
	})

	Swal.fire({
		title: 'Add Task Success !',
		allowOutsideClick: false,
		icon: 'success'
	}).then((result) => {
		if (result) {
			goToViewTaskState()
		}
	})
}

const isEditTaskMode = ref(false)
const selectedTask = ref({
	title: '',
	description: '',
	dueDate: ''
})

const currentTaskIndex = ref(null)
const goToEditTask = (taskIndex) => {
	currentTaskIndex.value = taskIndex
	selectedTask.value = toDoList.value[taskIndex]
	currentTodoState.value = 'editTask'
	isEditTaskMode.value = true
}

const editTask = (taskForm) => {
	taskForm.dueDate = new Date(taskForm.dueDate).toISOString()
	toDoList.value[currentTaskIndex.value] = {
		...taskForm,
		id: selectedTask.value.id,
		createAt: selectedTask.value.createAt
	}

	Swal.fire({
		title: 'Edit Task Success !',
		allowOutsideClick: false,
		icon: 'success'
	}).then((result) => {
		if (result) {
			goToViewTaskState()
		}
	})
}

const deleteTask = (taskIndex) => {
	Swal.fire({
		title: 'Are you sure?',
		text: 'Do you want to delete this task',
		icon: 'warning',
		showCancelButton: true,
		confirmButtonText: 'Confirm',
		reverseButtons: true
	}).then(async (result) => {
		if (result.isConfirmed) {
			toDoList.value.splice(taskIndex, 1)

			Swal.fire({
				title: 'Delete Task Success !',
				allowOutsideClick: true,
				icon: 'success'
			})
		}
	})
}

const checkedTask = (taskIndex, updatedTask) => {
	toDoList.value[taskIndex] = updatedTask
}

const webPageTitle = computed(() => {
	if (currentTodoState.value === 'addTask') {
		return 'To-Do List (Create)'
	} else if (currentTodoState.value === 'editTask') {
		return 'To-Do List (Edit)'
	} else {
		return 'To-Do List'
	}
})

watch(currentTodoState, () => {
	useSeoMeta({
		title: webPageTitle.value
	})
})
useSeoMeta({
	title: webPageTitle.value
})
</script>
