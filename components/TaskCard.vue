<template>
	<div>
		<div
			class="flex gap-2 px-3 py-4 bg-white border border-l-4 border-gray-400 rounded-lg shadow-md"
			:class="isCompleted ? 'border-l-green-400' : 'border-l-blue-400'"
		>
			<div class="flex items-start mt-1">
				<input
					type="checkbox"
					:id="task.id"
					v-model="isCompleted"
					@change="emitCheckboxInput"
				/>
			</div>

			<div class="flex flex-col w-full overflow-x">
				<div class="flex justify-between">
					<div>
						<p class="font-semibold" :class="isCompleted ? ' line-through' : ''">
							{{ task.title }}
						</p>
						<p class="text-xs text-gray-400">
							create date: {{ new Date(task.createAt).toLocaleDateString() }}
						</p>
					</div>
					<div class="flex gap-2 w-fit">
						<PencilIcon
							@click="$emit('editTask')"
							class="w-4 h-4 text-gray-500 cursor-pointer hover:text-green-500"
						/>
						<TrashIcon
							@click="$emit('deleteTask')"
							class="w-4 h-4 text-gray-500 cursor-pointer hover:text-red-500"
						/>
					</div>
				</div>
				<hr class="mt-2 mb-3" />
				<div
					v-if="
						!description.expanded && task.description.length > maxDescriptionLength
					"
					class="text-sm text-gray-600 break-all"
				>
					{{ truncatedText }}...<span
						class="text-xs text-blue-400 cursor-pointer hover:underline"
						@click="toggleExpanded"
					>
						More</span
					>
				</div>

				<div
					v-else-if="description.expanded"
					class="text-sm text-gray-600 break-all"
				>
					{{ task.description
					}}<span
						class="text-xs text-blue-400 cursor-pointer hover:underline"
						@click="toggleExpanded"
					>
						Less</span
					>
				</div>
				<div v-else class="text-sm text-gray-600 break-all">
					{{ task.description }}
				</div>
				<span class="mt-3 text-sm text-red-500"
					>Due date: {{ new Date(task.dueDate).toLocaleDateString() }}</span
				>
			</div>
		</div>
	</div>
</template>
<script setup>
import { PencilIcon, TrashIcon } from '@heroicons/vue/24/outline'
const props = defineProps({
	task: {
		type: Object,
		require: true
	}
})

const isCompleted = ref(props.task.isCompletedTask)
const emits = defineEmits(['editTask', 'deleteTask', 'checked'])
const emitCheckboxInput = () => {
	props.task.isCompletedTask = isCompleted.value
	emits('checked', props.task)
}

const maxDescriptionLength = ref(150)

const truncatedText = computed(() =>
	props.task.description.slice(0, maxDescriptionLength.value)
)
const description = ref({
	expanded: false
})

const toggleExpanded = () => {
	description.value.expanded = !description.value.expanded
}
</script>
<style scoped></style>
