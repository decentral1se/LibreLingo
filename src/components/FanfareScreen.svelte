<script>
  import { scale } from "svelte/transition"
  import db from "../db"
  import sound from "../media/sound"
  import hotkeys from "hotkeys-js"
  import { onMount } from "svelte"
  import { goto } from "@sapper/app"
  import Mascot from "./Mascot"
  import TwitterButton from "./TwitterButton"

  export let rawChallenges
  export let courseURL
  export let skillId

  onMount(async () => {
    sound.fanfare.play()
  })

  onMount(() => {
    const _id = `skills/${skillId}`
    db.get(_id)
      .then(function(doc) {
        db.put({
          ...doc,
          practiced: [
            ...(doc.practiced || []),
            {
              at: new Date().valueOf()
            }
          ]
        })
      })
      .catch(function() {
        db.put({
          _id,
          practiced: [
            {
              at: new Date().valueOf()
            }
          ]
        })
      })
  })

  onMount(() => {
    hotkeys.unbind("enter")
    hotkeys("enter", () => {
      goto(courseURL)
    })
  })
</script>

<section class="hero is-fullheight-with-navbar">
  <div class="hero-body">
    <div class="container">
      <div class="columns" in:scale>
        <div class="column is-three-fifths">
          <div class="mascot">
            <Mascot />
          </div>
        </div>
        <div class="column is-centered-mobile">
          <h1 class="title is-size-2 is-size-3-mobile">Lesson completed!</h1>
          <h2 class="subtitle">
            You've completed {rawChallenges.length} challenges
          </h2>
          <a class="button primary is-medium" href="{courseURL}">
            Continue to course page
          </a>
          <div class="bottom">
            <h2 class="title is-5">Excited about LibreLingo?</h2>
            <TwitterButton />
          </div>
        </div>
      </div>
    </div>
  </div>
</section>

<style>
  @import "../variables";

  @include from($tablet) {
    .bottom {
      position: absolute;
      bottom: 0;
    }
  }

  @include until($tablet) {
    .mascot {
      width: 45%;
      margin: auto;
      margin-top: -3em;
    }

    .bottom {
      margin-top: 4em;
    }

    .is-centered-mobile {
      text-align: center;
    }
  }
</style>
