<script>
  import { onMount, tick } from 'svelte'
  import anime from 'animejs/lib/anime.es.js'
  import page from 'page'

  import homeTransitions from '../../transitions/home'
  import { mobile } from '../../stores'

  export let delay
  export let animateIn
  export let parentWidth
  export let parentHeight
  export let nextPage

  let height, width, clicked

  const onClick = async () => {
    clicked = true
    const home = $mobile ? parentHeight : parentWidth
    const button = $mobile ? height : width
    await homeTransitions.outro(home, button, $mobile)
    page('/' + nextPage)
  }

  onMount( async () => {
    await tick()
    if (animateIn) {
      const letters = document.querySelectorAll('.button-letter')
      
      const containerTransition = {
        targets: '.button-container',
        delay: delay,
        easing: 'easeOutExpo',
      }

      const letterTransition = {
        targets: letters,
        opacity: [0, 1],
        delay: anime.stagger(50, {start: delay, from: 'center'}),
        easing: 'easeOutExpo'
      }

      if ($mobile) {
        containerTransition.translateY = [innerHeight + 20, 0]
        letterTransition.translateY = [innerHeight + 20, 0]
      } else {
        containerTransition.translateX = [width + 20, 0]
        letterTransition.translateX = [width + 20, 0]
      }

      anime(containerTransition)
      anime(letterTransition)
    }
  })
</script>

<div class='button-container' class:clicked>
  <button on:click={onClick}
    class='right-button' 
    bind:clientWidth={width}
    bind:clientHeight={height}>
    {#each nextPage.toUpperCase().split("") as letter }
      <h3 class='button-letter'>{letter}</h3>
    {/each}
  </button> 
  <div class='hover-background-buffer'>
  </div>
</div>
  
<style>
  .button-container {
    position: relative;
    right: -20px;
    height: 100%;
    display: flex;
    transition: right 0.3s ease-in-out;
  }

  .right-button {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex: 1;
    margin: 0;
    border: none;
    border-left: 5px solid aliceblue;
    background-color: rgba(0, 0, 0, 35%);
    transition: background-color 0.3s ease-in-out;
    cursor: pointer;
  }

  .button-letter {
    margin: 10px 30px;
    color: aliceblue;
    transition: color 0.3s ease-in-out;
  }

  .hover-background-buffer {
    background: aliceblue;
    width: 20px;
    flex: 1;
    transition: opacity 0.3s ease-in-out;
  }
  
  .button-container:hover {
    right: 0px;
  }

  .button-container:hover .right-button, 
  .button-container.clicked .right-button {
    background-color: aliceblue;
  }
  
  .button-container:hover .button-letter, 
  .button-container.clicked .button-letter {
    color: black;
    text-shadow: none;
  }
  
  .button-container.clicked {
    right: -20px;
  }

  .button-container.clicked .hover-background-buffer {
    opacity: 0;
  }

  @media only screen and (max-width: 1200px) {
    .button-container {
      flex-direction: column;
      right: 0;
      height: unset;
      width: 100%;
      bottom: -20px;
      transition: bottom 0.3s ease-in-out;
    }

    .button-container:hover {
      bottom: 0;
    }

	  .right-button {
      flex-direction: row;
      border-left: none;
      border-top: 2px solid aliceblue;
    }

    .button-letter {
      margin: 15px 10px;
    }

    .hover-background-buffer {
      min-height: 20px;
      width: 100%;
    }

    .button-container.clicked {
      bottom: -20px;
      right: 0;
    }
  }
</style>