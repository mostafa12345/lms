<template>
	<header
		v-if="!fromLesson"
		class="sticky flex items-center justify-between top-0 z-10 px-3 py-2.5 sm:px-5 text-white shadow-md" style="background: linear-gradient(to left, #b31313, #8c0c0c, black);"
	>
		<Breadcrumbs :items="breadcrumbs" />
	</header>
	<div
		class="md:w-7/12 md:mx-auto mx-4 py-10"
		:class="{ 'pt-4 md:w-full': fromLesson }"
	>
		<Quiz :quizName="quizID" />
	</div>
</template>
<script setup>
import Quiz from '@/components/Quiz.vue'
import { createResource, Breadcrumbs } from 'frappe-ui'
import { computed, inject, onMounted, ref } from 'vue'
import { useRouter } from 'vue-router'
import { updateDocumentTitle } from '@/utils'

const user = inject('$user')
const router = useRouter()
const fromLesson = ref(false)

onMounted(() => {
	if (!user.data) {
		router.push({ name: 'Courses' })
	}

	if (new URLSearchParams(window.location.search).get('fromLesson')) {
		fromLesson.value = true
	}
})

const props = defineProps({
	quizID: {
		type: String,
		required: true,
	},
})

const title = createResource({
	url: 'frappe.client.get_value',
	params: {
		doctype: 'LMS Quiz',
		fieldname: 'title',
		filters: {
			name: props.quizID,
		},
	},
	auto: true,
})

const breadcrumbs = computed(() => {
	return [{ label: __('Quiz Submission') }, { label: title.data?.title }]
})

const pageMeta = computed(() => {
	return {
		title: title.data?.title,
		description: __('Quiz Submission'),
	}
})

updateDocumentTitle(pageMeta)
</script>
