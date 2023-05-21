<script>
    import { createEventDispatcher } from 'svelte';
    import { onMount, onDestroy } from "svelte";

    const dispatch = createEventDispatcher();
    let name;
    let key;
    let error;
    function createRoom(){
        if(!name || !key || name.length == 0 || key.length == 0){
            error = "Please enter your First Name and OpenAI Key";
            return;
        }
        dispatch('message', {type:"createroom", name:name, openai_key:key});
    }

</script>
<div class="is-pulled-left">
<center><span class="is-size-4" style="margin-top: 5%;"> Create Room</span></center> 
   <form >
       <div class="field">
         <label class="label"> First Name</label>
         <div class="control">
           <input class="input" bind:value={name} type="text" placeholder="John">
         </div>
       </div>
       <div class="field">
         <label class="label">OpenAI Key</label>
         <div class="control">
           <input class="input" type="password" bind:value={key} placeholder="Enter your OpenAI key">
         </div>
         <p class="help">This app doesn't save your key. However, it trasmits to the your friend's system, so that messages are processed using AI. Here's a link on how to generate the OpenAI key.

         </p>
       </div>
       {#if error}
       <div class="notification is-danger is-light">
           {error}
        </div>
       {/if}
       <div class="field">
         <div class="control">
           <button class="button is-info" on:click={() => createRoom()}>Create Room</button>
           <!-- <button class="button is-info" on:click={() => dispatch('message', {type:"navigate", page:"exchangeoffers"})}>Create Room</button> -->

        </div>
       </div>
   </form>
</div>