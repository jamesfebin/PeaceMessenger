<script>
    import { createEventDispatcher } from 'svelte';
    const dispatch = createEventDispatcher();
</script>

<div>


<p style="text-align: justify !important; margin-top:4%">
    Peace Messenger serves as your guiding light, assisting
    you with AI in expressing your heartfelt emotions and
    needs to those who are dear to you. 
    <br /> 
    <center><img src="/peace_messenger/hero.png" style="height:250px !important"/></center> 

    <br />    

    This project is an attempt to see if AI can make conversations better and help people understand and respect each other more.

</p>
<br />

<div class="buttons">
    <button class="button is-info" on:click={() => dispatch('message', {type:"navigate", page:"createroom"})}> <span>Create Room </span></button>
    <button class="button is-primary" on:click={() => dispatch('message', {type:"navigate", page:"joinroom"})}>Join Room </button>
</div>





<div
    class="notification is-info is-light"
    style="text-align: justify !important;"
>
    <small>
        Though messages are transmitted with WebRTC peer-to-peer communication, it's also processed with OpenAI. Hence,
        it's not recommended to share any sensitive information.
    </small>
</div>
</div>