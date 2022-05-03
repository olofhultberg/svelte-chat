<script >
	import {onMount} from 'svelte';
	import Pusher from 'pusher-js';

	let username='Guest user';
	let message='';
	let messages =[{
		"username":"John Doe",
		"message": "Hi there!"
	}];

	onMount(()=>{
		// Enable pusher logging - don't include this in production
		Pusher.logToConsole = true;

		const pusher = new Pusher('451f9189d7457b091a53', {
		cluster: 'eu'
		});

		const channel = pusher.subscribe('chat');
		channel.bind('message', data => {
			messages = [...messages, data];
		});
	})

	const submit = async ()=>{
		await fetch('https://pusher-api.herokuapp.com/api/messages', {
			method: 'POST',
			headers: {'content-type': 'application/json'},
			body: JSON.stringify({
				username,
				message
			})
		})
		message='';
	}
</script>

<div class="container">
	<div class="d-flex flex-column align-items-stretch flex-shrink-0 bg-white" >
		<div class="d-flex align-items-center p-3 link-dark text-decoration-none border-bottom">
			<input class="fs-5 fw-semibold" bind:value={username}/>
		</div>
		<div class="list-group list-group-flush border-bottom scrollarea">
			{#each messages as msg}
			<div class="list-group-item list-group-item-action py-3 lh-tight">
				<div class="d-flex w-100 align-items-center justify-content-between">
					<strong class="mb-1">{msg.username}</strong>
				</div>
				<div class="col-10 mb-1 small">{msg.message}</div>
			</div>
			{/each}
		</div>
  	</div>
	  <form on:submit|preventDefault={submit}>
		  <input class="form-control" placeholder="Write a message" bind:value={message}/>
	  </form>
</div>

<style>
	.scrollarea{
		min-height: 500px;
	}
</style>