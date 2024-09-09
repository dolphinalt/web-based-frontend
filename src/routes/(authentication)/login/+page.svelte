<script lang="ts">
    import { onMount } from 'svelte';
    import { SERVER } from "$lib/vars.js";

    let currentID = "not specified.";
    let name: string;

    onMount(() => {
        currentID = localStorage.getItem('id') ?? "not specified.";
    })

    async function saveID() {
        if (name == "") name = currentID
        localStorage.setItem('id', name)
        currentID = localStorage.getItem('id') ?? "not specified.";
        console.log(localStorage.getItem('id'))
        const raw = JSON.stringify({ uid: localStorage.getItem('id') })
        console.log(raw)
        console.log(SERVER + 'test_user')
        fetch(SERVER + 'test_user', {
            redirect: 'follow',
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: raw
        })
        .then(response =>
            response.json()
        )
        .then(data => {
            if (data.err) {
                alert(data.err)
                if (data.err.includes('started')) {
                    window.location.pathname = '/challenges'
                } else if (data.err.includes('now start')) {
                    window.location.pathname = '/start'
                }
            }
        })
        .catch(error => {
            console.log(error)
            alert("Error occured")
        })
    }

</script>

<!-- Email and Password Box -->
<form on:submit|preventDefault={saveID}>
    <p>Your current ID is {currentID}</p>
    <input bind:value={name} type="text" placeholder="Updated ID (from Aeacus)" class="input w-full max-w-xs " />

    <!-- Submit Button -->
    <input type="submit" value="Login" class="btn btn-primary my-4 w-full" />
</form>