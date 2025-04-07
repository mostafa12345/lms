<template>
	<button
		v-if="link && !link.onlyMobile"
		class="flex h-8 cursor-pointer items-center rounded-lg transition-all duration-300 ease-in-out focus:outline-none"
		:class="[
			isActive ? 'bg-[#b31313] text-white shadow-md' : 'hover:bg-gray-100 text-gray-800',
			isCollapsed ? 'justify-center' : 'px-3'
		]"
		@click="handleClick"
	>
		<div
			class="flex items-center w-full transition-all duration-300 ease-in-out group"
			:class="isCollapsed ? 'p-1 relative' : 'py-1.5'"
		>
			<Tooltip :text="link.label" placement="right">
				<slot name="icon">
					<span class="grid h-5 w-6 flex-shrink-0 place-items-center">
						<component
							:is="icons[link.icon]"
							class="h-4 w-4 stroke-1.5"
							:class="isActive ? 'text-white' : 'text-gray-600'"
						/>
					</span>
				</slot>
			</Tooltip>

			<span
				class="flex-shrink-0 text-sm transition-all duration-300 ease-in-out"
				:class="isCollapsed ? 'ml-0 w-0 overflow-hidden opacity-0' : 'ml-2 w-auto opacity-100'"
			>
				{{ __(link.label) }}
			</span>

			<!-- Count badge -->
			<span
				v-if="link.count"
				class="ml-auto text-xs"
				:class="[
					isCollapsed && link.count > 9 ? 'absolute top-[2px] right-0 bg-white' : '',
					isActive ? 'text-white' : 'text-gray-400'
				]"
			>
				{{ link.count }}
			</span>

			<!-- Edit/Delete controls -->
			<div
				v-if="showControls && !isCollapsed"
				class="flex items-center space-x-2 ml-auto text-xs text-gray-400 group-hover:visible invisible"
			>
				<component
					:is="icons['Edit']"
					class="h-3 w-3 stroke-1.5"
					@click.stop="openModal(link)"
				/>
				<component
					:is="icons['X']"
					class="h-3 w-3 stroke-1.5"
					@click.stop="deletePage(link)"
				/>
			</div>
		</div>
	</button>
</template>

<script setup>
import { Tooltip } from 'frappe-ui'
import { computed } from 'vue'
import { useRouter } from 'vue-router'
import * as icons from 'lucide-vue-next'

const router = useRouter()
const emit = defineEmits(['openModal', 'deletePage'])

const props = defineProps({
	link: {
		type: Object,
		required: true,
	},
	isCollapsed: {
		type: Boolean,
		default: false,
	},
	showControls: {
		type: Boolean,
		default: false,
	},
})

function handleClick() {
	if (router.hasRoute(props.link.to)) {
		router.push({ name: props.link.to })
	} else if (props.link.to) {
		window.location.href = `/${props.link.to}`
	}
}

const isActive = computed(() => {
	return props.link?.activeFor?.includes(router.currentRoute.value.name)
})

const openModal = (link) => {
	emit('openModal', link)
}

const deletePage = (link) => {
	emit('deletePage', link)
}
</script>

