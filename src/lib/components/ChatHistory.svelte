<script lang="ts">
	import Icon from '@iconify/svelte';
	import { marked } from 'marked';
	import { onMount, tick } from 'svelte';

	const { chatHistory: chatHistoryProp, isStreaming } = $props<{
		chatHistory: { text?: string; role: 'user' | 'agent' }[];
		isStreaming: boolean;
	}>();

	let chatHistory = $state(chatHistoryProp);

	let chatContainer: HTMLDivElement;

	const scrollToBottom = async (node: HTMLDivElement) => {
		node.scroll({ top: node.scrollHeight, behavior: 'smooth' });
	};

	onMount(() => {
		scrollToBottom(chatContainer);
	});

	$effect(() => {
		chatHistory = [...chatHistoryProp];

		const autoscroll =
			chatContainer &&
			chatContainer.offsetHeight + chatContainer.scrollTop > chatContainer.scrollHeight - 50;
		console.log(autoscroll);
		if (autoscroll) {
			tick().then(() => scrollToBottom(chatContainer));
		}
	});
</script>

{#if !chatHistory || chatHistory.length === 0}
	<div class="flex h-96 items-center justify-center">
		<div class="flex flex-col items-center gap-4 p-8 text-center">
			<Icon icon="ri:movie-ai-fill" class="h-16 w-16" />
			<p class="text-2xl font-semibold">What will you be interested to watch today?</p>
		</div>
	</div>
{/if}

<div
	bind:this={chatContainer}
	class="flex w-full max-w-4xl flex-1 flex-col gap-4 overflow-y-auto p-4 text-lg"
>
	{#each chatHistory as chat, index}
		{#if chat.role === 'user'}
			<div class="bg-base-300 self-end rounded-2xl rounded-tr-none p-3 px-4">
				{chat.text}
			</div>
		{/if}
		{#if chat.role === 'agent'}
			<div class="flex flex-col gap-2 py-4 pr-2 md:flex-row">
				<Icon icon="mingcute:ai-fill" class="h-6 w-6" />
				<div class="prose-lg w-full flex-1">
					{@html marked(chat.text!)}
					{#if isStreaming && index === chatHistory.length - 1}
						<span class="loading loading-dots loading-lg"></span>
					{/if}
				</div>
			</div>
		{/if}
	{/each}
</div>
