<script>
  import { createEventDispatcher } from "svelte"
  import Tab from './Tab.svelte'
  export let stories = []
  const dispatch = createEventDispatcher()
  const default_bg = "/cat-loading-box.gif"
  let bgType = "color"
  let bg = "#f2f2f2"
  let index = 0
  let tabs = []
  const updateCard = () => {
    console.log("update card", index)
    let card = stories[index]
    console.log("card", card)
    bgType = stories[index].bgType
    bg = stories[index].bg
    tabs[index].startTimer()
  }
  const selectTab = (e) => {
    let {tabId} = e.detail
    console.log("select tab", tabId)
    //complete previous tabs
    for (let i = 0; i < tabId; i++) {
      tabs[i].complete()
    }
    //clear next tabs
    for (let i = tabId; i < stories.length; i++) {
      tabs[i].resetTime()
    }
    index = tabId
    updateCard()
  }
  const updateStories = (s) => {
    if (Array.isArray(s) && s.length) {
      console.log("update stories", s.length)
      index = 0
      stories = s
      bgType = stories[index].bgType
      bg = stories[index].bg
    }
  }
  const nextTab = (e) => {
    console.log("tab finished", e.detail)
    index++
    if (index >= stories.length) {
      dispatch("finished")
    } else updateCard()
  }
  const handleError = (e) => {
    e.preventDefault()
    bg = default_bg
  }
  const openRepo = (e) => {
    console.log("click")
    window.open("https://github.com/daniel-ordonez/catstories", "_blank")
  }
  $: updateStories(stories)
</script>
<div class="card">
  <section class="card__content">
    <div class="card__top">

      <div class="card__tabs">
        {#each stories as item, i (item.id)}
        <Tab bind:this={tabs[i]} t={item.t || 3} tabId={i} isActive={index === i} on:select={selectTab} on:finished={nextTab}></Tab>
        {/each}
      </div>
    </div>
    <div class="card__bg">
      {#if bgType === "video"}
        <video class="bg__video" src="{bg}"></video>
      {:else if bgType === "image"}
        <img class="bg__img" src="{bg}" on:error="{handleError}">
      {:else}
        <div class="bg__color" style="background: {bg};"></div>
      {/if}
    </div>
  </section>
  <section class="card__action">
    <span>
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-chevron-up"><polyline points="18 15 12 9 6 15"></polyline></svg>
    </span>
    <button on:click={openRepo}>
      <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-github">
        <path d="M9 19c-5 1.5-5-2.5-7-3m14 6v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 20 4.77 5.07 5.07 0 0 0 19.91 1S18.73.65 16 2.48a13.38 13.38 0 0 0-7 0C6.27.65 5.09 1 5.09 1A5.07 5.07 0 0 0 5 4.77a5.44 5.44 0 0 0-1.5 3.78c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 9 18.13V22">
        </path>
      </svg>
    </button>
  </section>
</div>

<style>
.card {
  --card-action-height: 80px;
  --card-height: 728px;
  height: calc(var(--card-height) + var(--card-action-height));
  display: grid;
  grid-auto-flow: column;
  grid-template-rows: 1fr var(--card-action-height);
}
.card__content {
  height: var(--card-height);
  width: calc(var(--card-height)/16 * 9);
  background-color: white;
  height: 100%;
  position: relative;
  border-radius: 16px;
  overflow: hidden;
}
.card__top{
  padding: 8px;
  position: absolute;
  top: 0;
  width: 100%;
  display: flex;
  flex-direction: column;
  z-index: 1;
}
.card__tabs {
  display: flex;
  flex-direction: row;
  column-gap: 4px;
  height: 8px;
  flex-basis: 100%;
}
.card__bg {
  height: calc(100% + 2px);
  position: relative;
}
.card__bg>*{
  position: absolute;
  height: 100%;
  width: 100%;
  object-fit: cover;
}
.bg__color {
  height: var(--card-action-height);
  position: absolute;
  width: 100%;
  height: 100%;
}
.card__action {
  justify-content: center;
  align-items: center;
  display: flex;
  column-gap: 8px;
  padding-left: 12px;
  flex-direction: column;
}
.card__action input[type=text] {
  border: solid 1px white;
  height: 36px;
  border-radius: 18px;
  width: 75%;
  background: none;
  outline: none;
  appearance: none;
  padding: 12px 24px;
  color: inherit;
  font-size: 16px;
  font-weight: 600;
}
.card__action button {
  outline: none;
  appearance: none;
  border: none;
  background-color: white;
  color: #020202;
  width: 90px;
  height: 36px;
  border-radius: 18px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
}
.card__action>span {
  animation: point 1.5s infinite;
  animation-delay: 4s;
  animation-timing-function: ease;
    animation-fill-mode: both;
    animation-timing-function: ease-in-out;
}
@keyframes point {
  0% {
    transform: translateY(0);
    box-shadow: 0 1px 2px rgba(0,0,0,.15);
  }
  100% {
    transform: translateY(-10px);
    box-shadow: 0 4px 20px rgba(0,0,0,.1);
  }
}


</style>