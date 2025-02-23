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
		const eventSource = new EventSource('http://localhost:3030/v1/chat/events');
		console.log(eventSource);

		eventSource.onopen = () => {
			console.log('EventSource connection opened.');
		};

		eventSource.onmessage = (event) => {
			const message = JSON.parse(event.data);
			console.log('Received message:', message);

			if (message.type === 'end') {
				isStreaming = false;
				return;
			}

			if (message.type === 'chat') {
				if (isStreaming) {
					const lastMessage = chatHistory[chatHistory.length - 1];
					chatHistory = [
						...chatHistory.slice(0, chatHistory.length - 1),
						{ text: `${lastMessage.text}${message.text}`, role: 'agent' }
					];
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
		const response = await fetch('http://localhost:3030/v1/chat/message', {
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

<div class="flex h-full w-full flex-col items-center">
	<ChatHistory {chatHistory} />
	<ChatInput bind:input {sendMessage} />
</div>
