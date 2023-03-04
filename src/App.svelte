<script>
  import Card from './lib/Card.svelte'
  let stories = [{
    bg: "/cat-loading-box.gif", bgType: "image", t: 3, id: new Date().getTime() + Math.random()
  }]
  let nextStories = []
  let tags = []
  /*
  fetch("https://cataas.com/api/tags")
  .then(res => res.json())
  .then(data => {
    tags = data
  })
  */
  const createRandomStories = () => {
    let s = []
    let n = Math.floor(Math.random() * 10) + 1
    for (let i = 0; i < n; i++) {
      /*
      let tag
      if (tags.length) {
        let i = Math.floor(Math.random() * tags.length)
        tag = tags[i]
        console.log(i, tag)
      }
      */
      let id = new Date().getTime() + Math.random()
      let t = Math.floor(Math.random() * 7) + 3
      s.push({
        bg: `https://picsum.photos/${1080 + Math.floor(Math.random() * 100)}/1920`,
       //bg: `https://cataas.com/cat/${tag? tag+'/':''}says/${tag? tag:t+'s'}`, 
        bgType: "image",
        id,
        t
      })
    }
    return s
  }
  const preloadStories = () => {
    nextStories = createRandomStories()
    let images = []
    nextStories.map(s => {
      let i = new Image()
      i.src = s.bg
      images.push(i)
    })
  }
  const reloadStories = () => {
    stories = [...nextStories]
    preloadStories()
  }
  setTimeout(preloadStories, 500)
  let card = null
</script>

<main>
  <Card bind:this={card} stories={stories} on:finished={reloadStories}></Card>
</main>

<style>
main {
  width: 100vw;
  display: flex;
  justify-content: center;
  align-items: center;
}
</style>
