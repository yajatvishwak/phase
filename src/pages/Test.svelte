<script>
  import { onMount } from "svelte";
  import YoutubePlayer from "youtube-player";

  export let videoId = "5q87K1WaoFI";
  $: player && player.loadVideoById(videoId);

  let player;
  let targetPage = 1;
  let options = {
    playerVars: {
      autoplay: 1,
    },
  };
  let timestamps = {
    33: 2,
    250: 3,
    525: 4,
    892: 5,
  };
  let timer;
  let currtime = 0.0;
  async function startTimer() {
    currtime = await player.getCurrentTime();
    timer = setInterval(() => {
      currtime = parseFloat((currtime + 1.0).toFixed(2));
      console.log(currtime);
    }, 1000);
  }
  function stopTimer() {
    clearInterval(timer);
  }
  $: {
    let maxKey = -1;
    for (let key in timestamps) {
      let key2 = parseFloat(key);
      if (maxKey < 0 || key2 < currtime) {
        maxKey = Math.max(maxKey, key2);
      }
    }

    targetPage = timestamps[maxKey];
    console.log("Target:" + targetPage);
  }
  onMount(async () => {
    player = YoutubePlayer(player, options);
    player.loadVideoById(videoId);
    player.playVideo();
    player.on("stateChange", function (event) {
      console.log(event.data);
      if (event.data === 1) {
        startTimer();
      } else {
        stopTimer();
      }
    });
  });
</script>

<div class="grid grid-cols-2 p-3 gap-5">
  <div
    id="youtube-player"
    class="w-full h-[500px] rounded-3xl"
    bind:this={player}
  />
  <div class="my-5 h-[1000px]">
    {#key targetPage}
      <!-- svelte-ignore a11y-missing-attribute -->
      <object
        data={"http://localhost:37085/test.pdf#page=" + targetPage}
        type="application/pdf"
        width="100%"
        height="100%"
      >
        <p>
          Alternative text - include a link <a href="myfile.pdf">to the PDF!</a>
        </p>
      </object>
    {/key}
  </div>
</div>
