<script>
  // @ts-nocheck

  import axios from "axios";
  import { onMount } from "svelte";

  let url;
  let embedurl;
  let currentTime;
  let files;
  let targetPage = 1;
  let deetsurl = "https://www.youtube.com/oembed";
  let title = "";
  let timestamps = {
    33: 2,
    250: 3,
    525: 4,
    892: 5,
  };

  let player;
  const yt = () => {
    var tag = document.createElement("script");
    tag.src = "https://www.youtube.com/iframe_api";
    var firstScriptTag = document.getElementsByTagName("script")[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
    //asd
    onYouTubeIframeAPIReady();
    function onYouTubeIframeAPIReady() {
      player = new YT.Player("player", {
        height: "390",
        width: "640",
        videoId: "M7lc1UVf-VE",
        playerVars: {
          playsinline: 1,
        },
        events: {
          onReady: onPlayerReady,
          onStateChange: onPlayerStateChange,
        },
      });
    }
    function onPlayerReady(event) {
      event.target.playVideo();
    }
    var done = false;
    function onPlayerStateChange(event) {
      if (event.data == YT.PlayerState.PLAYING && !done) {
        setTimeout(stopVideo, 6000);
        done = true;
      }
    }
    function stopVideo() {
      player.stopVideo();
    }
  };
  yt();
  async function getDetailsFromYoutube() {
    const { data } = await axios.get(
      deetsurl + "?" + "format=json&url=" + encodeURIComponent(url)
    );
    if (data) {
      title = data.title;
    }
    embedurl =
      "https://www.youtube.com/embed/" +
      url.substring(url.indexOf("?v=") + 3) +
      "?autoplay=1&enablejsapi=1";
  }
</script>

<section class="w-full grid grid-cols-2">
  <div>
    <div>Phase</div>
    <form on:submit|preventDefault={getDetailsFromYoutube} action="">
      <input class="border p-5 text-4xl" bind:value={url} type="text" />
      <button class="m-3 bg-teal-600 p-3 text-white" type="submit"
        >Submit
      </button>
    </form>
    Now Playing:
    <!-- svelte-ignore a11y-missing-attribute -->
    <!-- <iframe width="400" height="400" src={embedurl} /> -->
    <div id="player" />
  </div>

  <!-- <div class="my-5">
    {#if files}
     
      <div class="">
        <object
          data={URL.createObjectURL(files[0])}
          type="application/pdf"
          width="100%"
          height="100%"
        >
          <p>
            Alternative text - include a link <a href="myfile.pdf"
              >to the PDF!</a
            >
          </p>
        </object>
      </div>
    {:else}
      <form>
        <input type="file" name="" bind:files id="" />
      </form>
    {/if}
  </div> -->
  <div class="my-5 h-[1000px]">
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
  </div>
</section>
