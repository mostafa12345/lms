<template>
	<header
		v-if="!fromLesson"
		class="sticky flex items-center justify-between top-0 z-10 px-3 py-2.5 sm:px-5 text-white shadow-md" style="background: linear-gradient(to left, #b31313, #8c0c0c, black);"
	>
		<Breadcrumbs :items="breadcrumbs" />
	</header>
	<div class="overflow-hidden h-[calc(100vh-3.2rem)]">
		<Assignment
			:assignmentID="assignmentID"
			:submissionName="submissionName"
			:showTitle="!fromLesson"
		/>
	</div>
</template>
<script setup>
import { Breadcrumbs, createResource } from 'frappe-ui'
import { computed, inject, onMounted, onBeforeUnmount, ref } from 'vue'
import Assignment from '@/components/Assignment.vue'

const user = inject('$user')
const fromLesson = ref(false)

const props = defineProps({
	assignmentID: {
		type: String,
		required: true,
	},
	submissionName: {
		type: String,
		default: 'new',
	},
})

const title = createResource({
	url: 'frappe.client.get_value',
	params: {
		doctype: 'LMS Assignment',
		fieldname: 'title',
		filters: {
			name: props.assignmentID,
		},
	},
	auto: true,
})

onMounted(() => {
	if (!user.data) {
		window.location.href = '/login'
	}

	if (new URLSearchParams(window.location.search).get('fromLesson')) {
		fromLesson.value = true
	}
})

const breadcrumbs = computed(() => {
	let crumbs = [
		{
			label: 'Submissions',
			route: { name: 'AssignmentSubmissionList' },
		},
		{
			label: title.data?.title,
			route: {
				name: 'AssignmentSubmission',
				params: {
					assignmentID: props.assignmentID,
				},
			},
		},
	]
	return crumbs
})
</script>
