<script>
	function handleLogin() {
	  if (window.nostr && window.nostr.getPublicKey) {
		const publicKey = window.nostr.getPublicKey();
		console.log('Public Key:', publicKey);
	  } else {
		console.log('window.nost.getPublicKey() object not found.');
	  }
	}
  </script>
  
  <svelte:head>
	<title>Home</title>
	<meta name="description" content="Svelte demo app" />
  </svelte:head>
  
  <section>
	<div class="min-h-screen flex items-center justify-center">
		<div class="text-center">
		  <h1 class="text-4xl text-purple-800 font-bold mb-4">Relaying.io</h1>
		  <h2 class="text-2xl text-purple-500 mb-8">Not your relay, not your note.</h2>
		  <button class="px-8 py-4 bg-purple-700 text-white rounded-lg" on:click={handleLogin}>
			Login with Nostr 🤙
		  </button>
		</div>
	  </div>
  </section>
  
  <style lang="postcss">
	:global(html) {
	  background-color: theme(colors.purple.100);
	}
  </style>
  