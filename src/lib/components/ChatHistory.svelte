<script lang="ts">
	import Icon from '@iconify/svelte';
	import { marked } from 'marked';

	export let chatHistory: { text?: string; role: 'user' | 'agent' }[];
	export let isStreaming: boolean;
</script>

{#if chatHistory.length === 0}
	<div class="flex h-96 items-center justify-center">
		<div class="flex flex-col items-center gap-4 p-8 text-center">
			<Icon icon="ri:movie-ai-fill" class="h-16 w-16" />
			<p class="text-2xl font-semibold">What will you be interested to watch today?</p>
		</div>
	</div>
{/if}

<div class="flex w-full max-w-4xl flex-1 flex-col gap-4 p-4 text-lg">
	{#each chatHistory as chat, index}
		{#if chat.role === 'user'}
			<div class="bg-base-300 self-end rounded-2xl rounded-tr-none p-3 px-4 text-right">
				{chat.text}
			</div>
		{/if}
		{#if chat.role === 'agent'}
			<div class="flex flex-col gap-2 py-4 pr-2 md:flex-row">
				{#if isStreaming && index === chatHistory.length - 1}
					<span class="loading loading-ring loading-lg"></span>
				{:else}
					<Icon icon="mingcute:ai-fill" class="h-6 w-6" />
				{/if}
				<div class="prose-lg w-full flex-1">
					{@html marked(chat.text!)}
				</div>
			</div>
		{/if}
	{/each}
</div>
