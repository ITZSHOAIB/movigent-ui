<script lang="ts">
	import { onMount } from 'svelte';
	import { browser } from '$app/environment';
	import ChatHistory from '$lib/components/ChatHistory.svelte';
	import ChatInput from '$lib/components/ChatInput.svelte';

	type ChatMessage = {
		text?: string;
		role: 'user' | 'agent';
	};

	let chatHistory: ChatMessage[] = [];
	let input = '';
	let isStreaming = false;

	onMount(() => {
		if (!browser) return;

		console.log('Setting up EventSource...');
		const eventSource = new EventSource('/api/v1/chat/events');

		eventSource.onopen = () => {
			console.log('EventSource connection opened.');
		};

		eventSource.onmessage = (event) => {
			const message = JSON.parse(event.data);

			if (message.type === 'end') {
				isStreaming = false;
				return;
			}

			if (message.type === 'chat') {
				if (isStreaming) {
					const lastMessage = chatHistory[chatHistory.length - 1];
					lastMessage.text += message.text;
					chatHistory = [...chatHistory];
				} else {
					chatHistory = [...chatHistory, { text: message.text, role: 'agent' }];
					isStreaming = true;
				}
			}
		};

		eventSource.onerror = (error) => {
			console.error('EventSource failed:', error);
		};

		return () => {
			eventSource.close();
		};
	});

	const sendMessage = async (event: Event) => {
		event.preventDefault();
		if (input.trim() === '') return;

		chatHistory = [...chatHistory, { text: input, role: 'user' }];
		const response = await fetch('/api/v1/chat/message', {
			method: 'POST',
			headers: {
				'Content-Type': 'application/json'
			},
			body: JSON.stringify({ text: input })
		});

		if (response.ok) {
			input = '';
		} else {
			console.error('Failed to send message');
		}
	};
</script>

<div class="flex h-full w-full flex-col items-center overflow-y-hidden">
	<ChatHistory {chatHistory} {isStreaming} />
	<ChatInput bind:input {sendMessage} {isStreaming} />
</div>
