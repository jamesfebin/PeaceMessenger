<script>
	import { onMount, onDestroy } from "svelte";
	import Home from "../components/peacemessenger/Home.svelte";
	import CreateRoom from "../components/peacemessenger/CreateRoom.svelte";
	import ExchangeOffers from "../components/peacemessenger/ExchangeOffers.svelte";
	import JoinRoom from "../components/peacemessenger/JoinRoom.svelte";
	import Chat from "../components/peacemessenger/Chat.svelte";

	let localDescription;
	let remoteDescription;
	let exchangeError;
	let page = "home";
	let peer;
	let name;
	let peerName;
	let messages = [];
	let openai_key;
	let isBrowser = false;

  function handleBack(e) {
	e.preventDefault();
	localDescription=null;
	remoteDescription=null;
	openai_key=null; 
	name=null;
	peer=null;
	peerName=null;
	messages=[];
	page="home";
  }

  onMount(() => {
	isBrowser = true;
    window.addEventListener("popstate", handleBack);
  });

  onDestroy(() => {
	if(isBrowser)
    window.removeEventListener("popstate", handleBack);
  });


	function initiateChat(isInitiator) {
		peer = new SimplePeer({ initiator: isInitiator, trickle: false });
		peer._debug = console.log;
		peer.on("signal", (data) => {
			localDescription = btoa(JSON.stringify(data));
		});

		peer.on("connect", () => {
			console.log("Peer connected!");
			if (page != "chat") {
				page = "chat";
				peer.send(JSON.stringify({user:"system", type:"name", name:name }));
				
			}
			if(openai_key){
				peer.send(JSON.stringify({user:"system", type:"openai", openai_key:openai_key }));
			}
		});

		peer.on("data", (data) => {
			data = JSON.parse(data.toString());
			if(data.type == "name") {
				peerName = data.name;
				peer.send(JSON.stringify({user:"system", type:"name", name:name }));
			} else if(data.type=="message") {
				console.log(data);
				messages = [...messages, data];
			} else if(data.type=="openai") {
				console.log(data);
				openai_key = data.openai_key;
			}
		});

		peer.on("error", (err) => {
			if(page!="chat"){
				peer.signal(remoteDescription.trim());
			}
		});
	}

	function handleMessage(event) {
		console.log(event);
		if (event.detail.type == "navigate") {
			page = event.detail.page;
			if (page == "createroom") {
				window.history.pushState({"page":page}, null, null);
				initiateChat(true);
			} else if (page == "joinroom") {
				window.history.pushState({"page":page}, null, null);
				initiateChat(false);
			}
		} else if (event.detail.type == "join") {
			console.log("Joining");
			remoteDescription = event.detail.remoteDescription;
			if(event.detail.name)
			name = event.detail.name;
			try {
				console.log(atob(remoteDescription));
				peer.signal(JSON.parse(atob(remoteDescription.trim())));
			} catch (e) {
				console.log(e);
				exchangeError = "Please paste a valid code.";
			}
		} else if (event.detail.type == "createroom") {
			name = event.detail.name;
			page = "exchangeoffers";
			console.log("creating Room");
			console.log(name);
			openai_key = event.detail.openai_key;

		} else if (event.detail.type == "send") {
			console.log("sending");
			messages = [...messages, event.detail];
			peer.send(JSON.stringify({sender:event.detail.sender, message:event.detail.message, type:"message", timestamp:event.detail.timestamp}));
		}  else if(event.detail.type == "appendmessage"){
			messages = [...messages, event.detail];
		}
	}
</script>

<main >
	<div class="columns">
		<div class="column is-4" />
		<div class="column is-4">
			<div style="margin-top: 5%;">
				<img
					on:click={() => (page = "home")}
					style="cursor:default"
					src="/peace_messenger/logo_2x.png"
				/>
				{#if page == "home"}
					<Home on:message={handleMessage} {peer} />
				{:else if page == "createroom"}
					<CreateRoom on:message={handleMessage} {peer} />
				{:else if page == "exchangeoffers"}
					<ExchangeOffers
						on:message={handleMessage}
						offer={localDescription}
						{peer}
					/>
				{:else if page == "joinroom"}
					<JoinRoom
						on:message={handleMessage}
						offer={localDescription}
						{exchangeError}
						{peer}
					/>
				{:else if page == "chat"}
					<Chat name={name} peerName={peerName} messages={messages} openai_key={openai_key} on:message={handleMessage} />
				{/if}
			</div>
		</div>
		<div class="column is-4" />
	</div>
</main>
