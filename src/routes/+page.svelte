<script lang="ts">
	import { SHA256 } from "crypto-js";

	const backendUrl = import.meta.env.VITE_BACKEND_URL;
	const loginUrl = `${backendUrl}/login`;

	type NostrEvent = {
    id: string;
    pubkey: string;
    content: string;
    kind: number;
    created_at: number;
    tags: [string, string][];
    sig?: string;
}

	async function signAndAuthorizeEvent() {
        try {
            if (window.nostr) {
                const publicKey = await window.nostr.getPublicKey();
                const currentUrl = window.location.href;
                const eventData: any = {
                    kind: 27235,
                    tags: [
                        ["u", currentUrl + "login"],
                        ["method", "POST"],
                    ],
                    content: "",
                    pubkey: publicKey,
                    created_at: Math.floor(Date.now() / 1000),
                };

                // "id": <32-bytes lowercase hex-encoded sha256 of the serialized event data>,
                const sortedEventData = {
                    content: eventData.content,
                    created_at: eventData.created_at,
                    kind: eventData.kind,
                    pubkey: eventData.pubkey,
                    tags: eventData.tags,
                };

                const serializedJson = JSON.stringify(sortedEventData);
                const hash = SHA256(serializedJson).toString();
                const id = hash.toLowerCase();

                const event: NostrEvent = {
                    id: id,
                    kind: 27235,
                    tags: [
                        ["u", currentUrl + "login"],
                        ["method", "POST"],
                    ],
                    content: "",
                    pubkey: publicKey,
                    created_at: Math.floor(Date.now() / 1000),
                };

                const signedEvent = await window.nostr.signEvent(event);
                // Use the signedEvent for further processing or sending to the server
                console.log(signedEvent);

                // Send the authorization request to the backend using fetch
                const response = await fetch(loginUrl, {
                    method: "POST",
                    headers: {
                        "Content-Type": "application/json",
                        Authorization: `Nostr ${btoa(
                            JSON.stringify(signedEvent)
                        )}`,
                    },
                    body: JSON.stringify({ event: signedEvent }),
                });

                // Handle the authorization response
                if (response.ok) {
                    console.log("Successfully logged in with Nostr")
                } else {
                    console.log("Error logging in with Nostr");
                }
            } else {
                console.log("No nostr extension available");
            }
        } catch (error) {
            console.log("Error signing event");
        }
    }

    // Call the signAndAuthorizeEvent function directly when needed
    function login() {
        signAndAuthorizeEvent();
        // Additional login logic
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
		  <button class="px-8 py-4 bg-purple-700 text-white rounded-lg" on:click={login}>
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
  