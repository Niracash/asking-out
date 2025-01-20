<script lang="ts">
    import { onMount } from 'svelte';

    const gifs: string[] = [
        './bubu-dudu-heart.gif',
        './fake-act.gif',       // First GIF
        './cute-lovely.gif',    // Second GIF
        './cry-sad.gif',        // Third GIF
    ];

    const messages: string[] = [
        'Will you go out with me?',
        'No, seriously',        // First message
        'Think about it one more time', // Second message
        'Please...',            // Third message
    ];

    let gif: string = gifs[0];
    let message: string = messages[0];

    let currentStage: number = 0;
    let isNoButtonAbsolute: boolean = false;
    let noButtonStyle: { top: string; left: string } = { top: '0', left: '0' };
    let showNoButton: boolean = true;
    let isPlaying: boolean = false;
    let volume: number = 1.0;

    let audio: HTMLAudioElement | null = null;

    onMount(() => {
        audio = document.querySelector('audio');
        if (audio) {
            audio.volume = volume;
        }
    });

    function handleYes(): void {
        gif = './loveee.gif';
        message = 'Hehe, I love you!';
        showNoButton = false;
    }

    function handleNo(): void {
        if (currentStage < gifs.length - 1) {
            currentStage++;
            gif = gifs[currentStage];
            message = messages[currentStage];
        } else {
            runAwayIfNeeded();
        }
    }

    function runAwayIfNeeded(): void {
        if (currentStage === gifs.length - 1) {
            if (!isNoButtonAbsolute) {
                isNoButtonAbsolute = true;
            }
            moveNoButton();
        }
    }

    function moveNoButton(): void {
        const x = Math.random() * 80 + 10;
        const y = Math.random() * 80 + 10;
        noButtonStyle = { top: `${y}%`, left: `${x}%` };
    }

    function togglePlay(): void {
        if (audio) {
            isPlaying = !isPlaying;
            if (isPlaying) {
                audio.play();
            } else {
                audio.pause();
            }
        }
    }

    function changeVolume(event: Event): void {
        if (audio) {
            const target = event.target as HTMLInputElement;
            volume = parseFloat(target.value);
            audio.volume = volume;
        }
    }

    $: noButtonInlineStyle = isNoButtonAbsolute
        ? `top: ${noButtonStyle.top}; left: ${noButtonStyle.left};`
        : '';
</script>

<div class="h-screen w-screen flex flex-col items-center justify-between">
    <div class="flex flex-col items-center text-center mt-auto mb-auto">
        <img src={gif} alt="Cute GIF" class="mb-4" />
        <h1 class="text-white font-comic text-6xl md:text-5xl mb-4 shadow-text">{message}</h1>

        <div class="flex gap-8 mt-4">
            <!-- Yes button -->
            <button
                class="bg-green-600 hover:bg-green-700 text-white py-4 px-8 rounded-lg text-2xl"
                on:click={handleYes}
            >
                Yes
            </button>

            {#if showNoButton}
                <button
                    class="bg-red-600 hover:bg-red-700 text-white py-4 px-8 rounded-lg text-2xl {isNoButtonAbsolute ? 'absolute' : ''}"
                    style={noButtonInlineStyle}
                    on:click={handleNo}
                    on:mouseover={runAwayIfNeeded}
                    on:focus={runAwayIfNeeded}
                >
                    No
                </button>
            {/if}
        </div>

    </div>

    <footer class="mt-auto py-4 text-center text-white text-lg flex flex-col items-center">
        <!-- Play/Stop Button -->
        <p class="text-white mb-2 shadow-text">Play music for more romantic vibe 🤍</p>
        <button
            class="bg-gray-600 hover:bg-gray-700 text-white py-2 px-4 rounded-full flex items-center gap-2"
            on:click={togglePlay}
        >
            {isPlaying ? 'Stop music' : 'Play music'}
        </button>

        <!-- Volume Slider -->
        <div class="flex items-center gap-2 mt-4">
            <label for="volume" class="text-white shadow-text">Volume:</label>
            <input
                type="range"
                id="volume"
                min="0"
                max="1"
                step="0.01"
                value={volume}
                on:input={changeVolume}
                class="w-40"
            />
        </div>

        <p class="mt-4 shadow-text">Made with SvelteKit by Nirakash</p>
    </footer>

    <audio loop>
        <source src="./piano.mp3" type="audio/mpeg" />
        Your browser does not support the audio element.
    </audio>
</div>

<style>
    @import url('https://fonts.googleapis.com/css2?family=Comic+Sans+MS:wght@400&display=swap');

    :global(body) {
        margin: 0;
        padding: 0;
        font-family: 'Comic Sans MS', cursive;
    }

    .shadow-text {
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.6);
    }
</style>