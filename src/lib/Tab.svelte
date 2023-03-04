<script>
  import { onMount } from "svelte"
  import { createEventDispatcher } from "svelte"
  export let t = 3//seconds 3 - 10
  export let isActive = false
  export let tabId = -1
  const dispatch = createEventDispatcher()
  let count = 0
  let tick = null
  let tack = 0
  $: progress = 100/(t * 60) * ((count * 60) + tack)
  const finished = () => {
    stopTimer()
    dispatch("finished", {tabId})
  }
  export const complete = () => {
    stopTimer()
    count = t
  }
  export const resetTime = (stop = true) => {
    console.log("reset time for", tabId, "stop", stop)
    if (stop) stopTimer()
    count = 0
    tack = 0
  }
  export const startTimer = () => {
    console.info(`start timer for ${tabId}`)
    tick = setInterval(() => {
      tack++
      if (tack === 60){
        count ++
        tack = 0
      }
      if (count >= t) {
        finished()
      }
    }, 1000/60)
  }
  onMount(() => {
    if (isActive) startTimer()
  })
  const stopTimer = () => {
    if (tick) {
      console.info(`stop timer for ${tabId}`)
      clearInterval(tick)
    }
    tick = null
  }
  const setActive =(v) => {
    console.log(`set active ${v} on tab ${tabId}`)
    isActive = v
    if (isActive === true) startTimer()
    else if (tick) stopTimer()
  }
  const selectTab = () => {
    if (isActive) {
      resetTime(false)
    } else {
      dispatch("select", {tabId})
    }
  }
  $: tabId
  //$: setActive(isActive)
</script>
<div class="tab" on:click={selectTab}>
  <div class="tab__bg" style="{`width:${progress}%`}"></div>
</div>

<style>
  .tab {
    position: relative;
    width: 100%;
    height: 6px;
    border-radius: 4px;
    overflow: hidden;
    background-color: rgba(255, 255, 255, 0.3);
    cursor: pointer;
  }
  .tab__bg {
    position: absolute;
    background-color: rgba(255, 255, 255, 0.5);
    height: 100%;
    left: 0;
  }
</style>