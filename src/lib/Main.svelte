<script>
  import '../lib/app.css';
  import {AudioPlayer} from 'svelte-mp3';
  import WestminsterChime from '../assets/westminster-chime.mp3';
  let bellTimesText = JSON.parse(window.localStorage.getItem('bellTimes') || '[]').join('\n') ?? `08:20:00
09:20:00
10:20:00
11:20:00
12:20:00
13:20:00
14:20:00
15:20:00
16:20:00
`
  $: bellTimes = bellTimesText.split('\n')
  $: window.localStorage.setItem('bellTimes', JSON.stringify(bellTimes))
  let time = new Date()
  let timeDisplay = `${zp(time.getHours())}:${zp(time.getMinutes())}:${zp(time.getSeconds())}`
  setInterval(() => {
    time = new Date()
    timeDisplay = `${zp(time.getHours())}:${zp(time.getMinutes())}:${zp(time.getSeconds())}`
    if (bellTimes.includes(timeDisplay)) {
      console.log(`belling [${timeDisplay}] ...`)
      play()
    }
  }, 100)
  function zp(/** @type {number} */num, padding=2 ) {
    if (String(num).length < padding) {
      return `0${num}`
    }
    return `${num}`
  }
  /** @type {HTMLAudioElement} */
  let audio
  /** @type {HTMLElement} */
  let controller
  /** @type {number} */
  let volume = 0.8
  /** @type {boolean} */
  let isInMute = false
  let isPlaying = false

  async function play() {
    if (isPlaying) {
      return
    }
    isPlaying = true
    return await audio.play()
  }

  function pause() {
    if (!isPlaying) {
      return
    }
    isPlaying = false
    audio.pause()
  }

  function toggleMute() {
    isInMute = !isInMute
    audio.muted = !audio.muted
  }
</script>
<div class="flex flex-col justify-evenly items-center content-end
  h-screen w-screen
  text-6xl font-mono
  md:justify-around md:items-center md:flex-row
">
<div>
  {timeDisplay}
</div>
<div class="text-xl">
  <label class="p-2">ปิดเสียง <input type="checkbox" bind:checked={isInMute} on:click="{toggleMute}"></label>
  <AudioPlayer 
    bind:audio 
    bind:controller 
    volume={volume}
    color="#fff"
    showNext={false}
    showPrev={false}
    showTrackNum={false}
    showShuffle={false}
    showRepeat={false}
    loop="no-repeat"
    shuffle={false}
    enableMediaSession={false}
    on:ended={()=>{
      audio.currentTime = 0
      isPlaying = false
    }}
    urls={[WestminsterChime]} />
    <audio>
      <source src="{WestminsterChime}" />
    </audio>
</div>
</div>
<style>
  .h-screen {
    height: -webkit-fill-available;
  }
</style>