<script>
     import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();
    export let offer;
    export let recievedOffer;
    function handleFocus(event) {
    event.target.select();
  }
</script>
<form style="margin-top:4%;">
    <div class="field">
        <div class="notification is-info is-light" style="font-weight: 500 !important; margin-bottom:2%">
            <small
            style="font-weight: 500 !important; "
                >Please forward the below code to your friend, inviting them to
                enter the room.
            </small></div
        >
        <div class="control">
            <textarea class="textarea" on:click={handleFocus} bind:value={offer} readonly></textarea>
        </div>
    </div>
    <br/>
    <div class="field">
        <label class="label" style="font-weight: 500 !important;"
            ><small
                >After your friend enters your code, another is produced for
                them. You need to input that here to connect with them.</small
            >
        </label>
        <div class="control">
            <textarea
                bind:value={recievedOffer}
                class="textarea"
                placeholder="Enter your friend's code here"
            />
        </div>
    </div>
 
    <div class="field">
        <div class="control">
            <button class="button is-info"  on:click={() =>
                dispatch("message", {
                    type: "join",
                    remoteDescription: recievedOffer,
                })}>Start Chatting</button>
        </div>
    </div>
</form>
