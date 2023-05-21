<script>
    import { createEventDispatcher } from "svelte";
    const dispatch = createEventDispatcher();
    export let offer;
    export let exchangeError;
    let name;
    let remoteDescription;
    function handleFocus(event) {
        event.target.select();
    }
</script>

<div>
<center>  <span class="is-size-4" style="margin-top: 5%;"> Join Room </span></center> 

<form style="margin-top:4%;">
    {#if offer}
        <div class="field">
            <div class="notification is-info is-light" style="font-weight: 550 !important; margin-bottom:2%">
                <small  style="font-weight: 550 !important;"
                    >Please forward the below code to your friend, once they
                    enter this, you will be automatically connected.
                </small></div
            >
            <div class="control">
                <textarea 
                    class="textarea"
                    on:click={handleFocus}
                    bind:value={offer}
                    readonly
                />
            </div>
        </div>

        <button class="button is-info"
            ><span>
                <i class="fas fa-spinner fa-spin" /> Waiting
            </span></button
        >
    {:else}
        <div class="field">
            <label class="label" style="font-weight: 500 !important;">
                <small
                    >Please paste the code shared by your friend who created the
                    room.
                </small></label
            >
            <div class="control">
                <textarea
                    class="textarea"
                    on:click={handleFocus}
                    bind:value={remoteDescription}
                />
            </div>
        </div>
        {#if exchangeError}
            <div class="notification is-danger is-light">
                <small>{exchangeError}</small>
            </div>
        {/if}

        <div class="field">
            <label class="label" style="font-weight: 500 !important;">
                <small
                    >Name
                </small></label
            >
            <div class="control">
                <textarea
                    class="input"
                    bind:value={name}
                    placeholder="John"
                />
            </div>
        </div>

        <div class="field">
            <div class="control">
                <button
                    class="button is-info"
                    on:click={() =>
                        dispatch("message", {
                            type: "join",
                            remoteDescription: remoteDescription,
                            name:name,
                        })}>Join Room</button
                >
            </div>
        </div>
    {/if}
</form>
</div>