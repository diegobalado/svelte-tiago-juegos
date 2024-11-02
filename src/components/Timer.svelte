<script>
    export let duration;
    export let progress;

    const HIGH_THRESHOLD = 0.6;
    const MEDIUM_THRESHOLD = 0.3;
    const LOW_THRESHOLD = 0.1;

    $: colorClass = progress > duration * HIGH_THRESHOLD ? 'bg-green-500' : 
                   progress > duration * MEDIUM_THRESHOLD ? 'bg-yellow-500' : 
                   progress > duration * LOW_THRESHOLD ? 'bg-orange-500' : 
                   'bg-red-500';
    $: progressWidth = (progress / duration) * 100;
</script>

<div class="relative mt-2 w-3/4 h-5 bg-gray-300 rounded-lg">
    <div
        class={`absolute h-full ${colorClass} transition-all duration-1000 rounded-lg`}
        style={`width: ${progressWidth}%`}
    ></div>
</div>