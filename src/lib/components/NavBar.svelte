<script>
	import Icon from '@iconify/svelte';
	import ThemeController from './ThemeController.svelte';
</script>

<div class="navbar bg-base-200 fixed top-0 shadow-sm">
	<div class="flex-1">
		<a class="btn btn-ghost text-xl" href="/">Movie Buddy</a>
	</div>
	<div class="flex gap-3 px-2">
		<ThemeController />
		<div class="flex-none">
			<div class="dropdown dropdown-end">
				<div tabindex="0" role="button" class="btn btn-ghost btn-circle avatar">
					<div class="rounded-full">
						<Icon icon="qlementine-icons:user-16" class="h-8 w-8" />
					</div>
				</div>
				<ul
					tabindex="0"
					class="menu menu-sm dropdown-content bg-base-300 rounded-box z-1 mt-3 w-52 p-2 shadow"
				>
					<li>
						<a class="justify-between">
							Profile
							<span class="badge">New</span>
						</a>
					</li>
					<li><a>Settings</a></li>
					<li><a>Logout</a></li>
				</ul>
			</div>
		</div>
	</div>
</div>
