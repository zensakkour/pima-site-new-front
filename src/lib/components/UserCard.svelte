<script>
	import { page } from '$app/stores';
	import { enhance } from '$app/forms';
	import Markdown from './Markdown.svelte';
	import UserProfileModal from './UserProfileModal.svelte';
	import { quintOut } from 'svelte/easing';
	import { scale } from 'svelte/transition';

	export let user;

	let showModal = false;
</script>

{#if showModal}
	<UserProfileModal {user} on:close={() => (showModal = false)}>
		<div class="user-profile">
			<div class="profile-header">
				<div class="initials">
					{user.prenom[0]}{user.nom[0]}
				</div>
				<h2>{user.nom} {user.prenom}</h2>
				<div class="l3">L3 {user.grad_year}</div>
			</div>
			<div class="profile-details">
				<div class="profile-card">
					<h4>Contact</h4>
					<p>Email: {user.email}</p>
					<p>Contact: {user.contact}</p>
					<p>Nationalité: {user.nationalite}</p>
				</div>
				<div class="profile-card">
					<h4>Parcours</h4>
					<p>Admis: {user.admis}</p>
					<p>Choisi: {user.choisi}</p>
					<p>Moyenne L2: {user.moyenneL2}</p>
					<p>Moyenne L3: {user.moyenneL3}</p>
				</div>
				<div class="profile-card description">
					<h4>Description</h4>
					<Markdown markdown={user.description} />
				</div>
				<div class="profile-card">
					<h4>Travails</h4>
					<p>{user.travails}</p>
				</div>
			</div>
			{#if $page.data.user?.admin}
				<div class="profile-card admin-controls">
					<h4>Admin Controls</h4>
					<h5>Autres (Lecture seule)</h5>
					<p>id: {user.id}</p>
					<p>login: {user.login}</p>
					<p>admin privileges: {user.admin}</p>
					<p>date création: {user.createdAt}</p>
					<p>derniere mise a jour: {user.updatedAt}</p>
					<h5>Visibilité</h5>
					<p>TODO</p>
					<h5>Admin actions</h5>
					<form method="POST" action="/restricted/user?/set_admin" use:enhance>
						<input type="hidden" name="login" value={user.login} />
						<button type="submit">Give admin permissions</button>
					</form>
					<form method="POST" action="/restricted/user?/unset_admin" use:enhance>
						<input type="hidden" name="login" value={user.login} />
						<button type="submit">Remove admin permissions</button>
					</form>
					<form method="POST" action="/restricted/user?/delete_user" use:enhance>
						<input type="hidden" name="login" value={user.login} />
						<button type="submit">Delete this user</button>
					</form>
				</div>
			{/if}
		</div>
	</UserProfileModal>
{/if}

<div
	class="user-card"
	role="button"
	tabindex="0"
	on:click={() => (showModal = true)}
	on:keydown={(e) => e.key === 'Enter' && (showModal = true)}
	transition:scale={{ duration: 300, easing: quintOut }}
>
	<div class="user-card-header">
		<div class="initials">
			{user.prenom[0]}{user.nom[0]}
		</div>
		<h3>{user.nom} {user.prenom}</h3>
		<div class="l3">L3 {user.grad_year}</div>
	</div>
	<div class="user-card-body">
		<p>{user.choisi}</p>
	</div>
</div>

<style>
	.user-card {
		background-color: var(--background-color);
		border-radius: 10px;
		box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
		padding: 20px;
		margin: 20px;
		display: flex;
		flex-direction: column;
		justify-content: space-between;
		transition: all 0.3s ease;
		cursor: pointer;
	}
	.user-card:hover {
		transform: scale(1.05);
	}
	.user-card-header {
		display: flex;
		align-items: center;
		margin-bottom: 20px;
	}
	.initials {
		width: 50px;
		height: 50px;
		border-radius: 50%;
		background-color: var(--accent-color);
		color: var(--background-color);
		display: flex;
		align-items: center;
		justify-content: center;
		font-size: 1.5rem;
		font-weight: bold;
		margin-right: 20px;
	}
	.user-card-header h3 {
		margin: 0;
		flex-grow: 1;
	}
	.l3 {
		font-size: 0.9rem;
		color: #aaa;
	}
	.user-card-body {
		flex-grow: 1;
	}
	.user-profile .profile-header {
		display: flex;
		align-items: center;
		margin-bottom: 2rem;
	}
	.user-profile .profile-header .initials {
		width: 80px;
		height: 80px;
		font-size: 2.5rem;
	}
	.user-profile .profile-header h2 {
		margin: 0;
		flex-grow: 1;
	}
	.user-profile .profile-details {
		display: grid;
		grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
		gap: 1rem;
	}
	.user-profile .profile-card {
		background-color: color-mix(in srgb, var(--background-color) 50%, transparent);
		padding: 1rem;
		border-radius: 10px;
	}
	.user-profile .profile-card h4 {
		margin-top: 0;
	}
	.description {
		grid-column: 1 / -1;
	}
	.admin-controls {
		background-color: #f0f0f0;
		border: 1px solid #ccc;
		padding: 1rem;
	}
	.admin-controls h4 {
		margin-top: 0;
	}
	.admin-controls h5 {
		margin-top: 1rem;
		margin-bottom: 0.5rem;
	}
	.admin-controls form {
		margin-bottom: 0.5rem;
	}
</style>
