<script>
    export let name;
    export let peerName;
    export let messages;
    export let openai_key;
    let message;
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
    import axios from "axios";

    let openAIRequested = false;


function exportConversation() {

  var text = messages.map((message) => {
        return message.sender + " : " + message.message + "\n";
    });
  text = text.join("");
  var element = document.createElement('a');
  element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
  element.setAttribute('download', 'peace_messenger_conversation.txt');

  element.style.display = 'none';
  document.body.appendChild(element);

  element.click();

  document.body.removeChild(element);
}
    function sendMessage() {
        console.log("sending message");
        let payload = {
            type: "send",
            sender: name,
            message: message,
            timestamp: Math.floor(Date.now() / 1000),
        };
        console.log(payload);
        dispatch("message", payload);
        message = "";
    }

    function handleKeyPress(event) {
    if (event.key === "Enter" && !event.shiftKey) {
      // Perform your action here
      sendMessage();
    }
  }

    async function openAIComplete(prompt) {
        console.log(openai_key);
        openAIRequested = true;
        let payload = {};
        try {
            const options = {
                method: "POST",
                url: "https://api.openai.com/v1/chat/completions",
                headers: {
                    "Content-Type": "application/json",
                    Authorization:
                        `Bearer ${openai_key}`,
                },
                data: {
                    model: "gpt-3.5-turbo",
                    messages: [{ role: "user", content: prompt }],
                },
            };
            const completion = await axios.request(options);
            //   console.log(completion.data);
            let openai_response = completion.data.choices[0].message.content;
            payload = {
                type: "appendmessage",
                sender: "Peace Messenger",
                message: openai_response,
                timestamp: Math.floor(Date.now() / 1000),
            };
            console.log(payload);
        } catch (e) {
            payload = {
                type: "appendmessage",
                sender: "Peace Messenger",
                message: "Unable to connect to OpenAI or Invalid API Key.",
                timestamp: Math.floor(Date.now() / 1000),
            };
        }
        dispatch("message", payload);
        openAIRequested = false;
    }

    async function seekPeace() {
        console.log("Seeking Peace");
        let plain_messages = messages.map((message) => {
            if (message.sender == "Peace Messenger") return "";
            return message.sender + " : " + message.message + "\n";
        });
        let script = plain_messages.join("");
        console.log(script);
        let last900 = script.split(" ").slice(-900).join(" ");
        let openAIInput = `Analyze the conversation provided then use principles from Marshall Rosenberg's 'Nonviolent Communication: A Language of Life'. Guide ${name} towards peaceful conflict resolution. Include example phrases in quotes as necessary. Limit your response to under six lines \n\n ==Conversation Start== \n ${last900} \n ==Conversation End== \n\n. Now guide ${name} what he needs to do.`;
        openAIComplete(openAIInput);
    }
</script>

<div>
    <center>
        <span class="tag is-primary is-rounded"
            ><b>Connected with {peerName}</b></span
        >
    </center>
    <div
        id="messages"
        style="height:50vh; overflow:scroll;margin-top:2%;display: flex; flex-direction: column-reverse;"
    >
        <small>
            {#each messages as message}
                {#if message.sender == "Peace Messenger"}
                    <div
                        class="notification is-primary is-light"
                        style="font-weight: 500 !important; margin-bottom:2%;"
                    >
                        <b>{message.sender}</b>
                        <small> (Only visible to you)</small> <br />
                        <p>{message.message}</p>
                        <br />
                    </div>
                {:else}
                    <b>{message.sender}</b> <br />
                    <p>{message.message}</p>
                    <br />
                {/if}
            {/each}
        </small>
    </div>

    <div class="footer-fixed">
        <form>
            <div class="field">
                <div class="control">
                    <textarea
                        class="textarea"
                        bind:value={message}
                        on:keyup={handleKeyPress} 
                        placeholder="Type your message here"
                    />
                </div>
            </div>
            <div class="field is-grouped">
                <div class="control">
                    <button
                        type="button"
                        on:click={() => sendMessage()}
                        class="button is-info">Send</button
                    >
                </div>
                <div class="control">
                    <button
                        type="button"
                        class="button is-primary"
                        on:click={() => seekPeace()}
                    >
                        {#if openAIRequested}
                            <span>
                                <i class="fas fa-spinner fa-spin" /> Please Wait
                            </span>
                        {:else}
                            Seek Peace
                        {/if}</button
                    >
                </div>
                <div class="control">
                    <button
                        type="button"
                        class="button"
                        on:click={()=>exportConversation()}>Export</button
                    >
                </div>
            </div>
        </form>
    </div>
</div>
