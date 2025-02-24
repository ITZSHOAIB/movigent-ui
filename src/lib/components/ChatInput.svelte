<script lang="ts">
	import Icon from '@iconify/svelte';
	export let input: string;
	export let sendMessage: (event: Event) => void;
	export let isStreaming: boolean;
</script>

<form on:submit={sendMessage} class="sticky bottom-0 w-full">
	<label for="chat" class="sr-only">Your message</label>
	<div class="bg-base-200 px-2 py-4">
		<div class="mx-auto flex max-w-4xl items-center">
			<textarea
				id="chat"
				rows="1"
				class="bg-base-100 mx-4 block h-16 w-full rounded-lg border border-gray-300 p-2.5 focus:border-blue-500 focus:ring-blue-500 dark:border-gray-600 dark:placeholder-gray-400 dark:focus:border-blue-500 dark:focus:ring-blue-500"
				placeholder="Your message..."
				bind:value={input}
			></textarea>
			<button
				disabled={isStreaming}
				type="submit"
				class="btn btn-primary inline-flex h-16 w-16 cursor-pointer justify-center p-2"
			>
				{#if isStreaming}
					<span class="loading loading-infinity loading-lg"></span>
				{:else}
					<Icon icon="ic:round-send" class="h-6 w-6" />
				{/if}
			</button>
		</div>
	</div>
</form>
