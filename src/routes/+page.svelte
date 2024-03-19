<script>
    import { onMount, onDestroy } from "svelte";
    import Vapi from "@vapi-ai/web";

    import { VAPI_API_KEY, VAPI_ASSISTANT_ID } from "$env/static/public";

    let vapi;
    let publicKey = VAPI_API_KEY;
    let assistantId = VAPI_ASSISTANT_ID;
    let isMuted = false;

    onMount(() => {
        vapi = new Vapi(publicKey);

        vapi.on("speech-start", () => {
            console.log("Speech has started");
        });

        vapi.on("speech-end", () => {
            console.log("Speech has ended");
        });

        vapi.on("call-start", () => {
            console.log("Call has started");
        });

        vapi.on("call-end", () => {
            console.log("Call has stopped");
        });

        vapi.on("volume-level", (volume) => {
            console.log(`Assistant volume level: ${volume}`);
        });

        vapi.on("message", (message) => {
            console.log(message);
        });

        vapi.on("error", (e) => {
            console.error(e);
        });
    });

    onDestroy(() => {
        if (vapi) {
            vapi.stop();
        }
    });

    function startCall() {
        vapi.start(assistantId);
    }

    function stopCall() {
        vapi.stop();
    }

    function toggleMute() {
        isMuted = !isMuted;
        vapi.setMuted(isMuted);
    }

    function sendMessage() {
        vapi.send({
            type: "add-message",
            message: {
                role: "system",
                content: "The user has pressed the button, say peanuts",
            },
        });
    }
</script>

<main>
    <button on:click={startCall}>Start Call</button>
    <button on:click={stopCall}>Stop Call</button>
    <button on:click={toggleMute}>{isMuted ? "Unmute" : "Mute"}</button>
    <button on:click={sendMessage}>Send Message</button>
</main>
