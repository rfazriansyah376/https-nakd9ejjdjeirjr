<div id="youtube-video-grid" class="video-grid"></div>


<style>
  body {
    margin: 0;
    font-family: Arial, sans-serif;
    background: #181818;
    color: #fff;
  }

  .video-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(240px, 1fr));
    gap: 12px;
    padding: 16px;
  }

  .video-item {
    position: relative;
    cursor: pointer;
    background: black;
    border-radius: 8px;
    overflow: hidden;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.2);
  }

  .video-wrapper {
    position: relative;
    width: 100%;
    padding-top: 56.25%;
    background: black;
  }

  .video-wrapper iframe {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    display: none;
  }

  .thumbnail {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: black;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .play-icon {
    width: 60px;
    height: 60px;
    display: flex;
    align-items: center;
    justify-content: center;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 50%;
    transition: background 0.3s ease;
  }

  .play-icon:hover {
    background: rgba(255, 255, 255, 0.4);
  }

  .play-icon svg {
    width: 40px;
    height: 40px;
    fill: white;
  }

  .video-item.playing .thumbnail {
    display: none;
  }

  .video-item.playing iframe {
    display: block;
  }

  .video-info {
    padding: 8px;
    font-size: 14px;
    text-align: center;
  }
</style>

<script>
function getRandomTitle() {
  const titleElement = document.getElementById("pilihan-judul");
  if (!titleElement) return "Video Menarik";
  
  const titleWords = titleElement.innerText.split(' '); 
  const randomTitles = [];

  while (randomTitles.length < 5 && titleWords.length > 0) {
    const randomIndex = Math.floor(Math.random() * titleWords.length);
    const randomWord = titleWords.splice(randomIndex, 1)[0];
    randomTitles.push(randomWord);
  }

  return randomTitles.join(' '); 
}

async function fetchVideosFromRSS() {
  const RSS_URL = "https://www.youtube.com/feeds/videos.xml?channel_id=UCn3jEcRKGWgn2iFJM45E1Rg";
  
  try {
    const response = await fetch(`https://api.rss2json.com/v1/api.json?rss_url=${encodeURIComponent(RSS_URL)}`);
    const data = await response.json();

    if (!data.items) return [];

    return data.items.map(item => ({
      title: item.title,
      videoId: item.link.split("v=")[1],
      uploadDate: item.pubDate
    }));
  } catch (error) {
    return [];
  }
}

document.addEventListener("DOMContentLoaded", async function () {
  const videoGrid = document.getElementById("youtube-video-grid");
  const allVideos = await fetchVideosFromRSS();

  allVideos.slice(0, 12).forEach((item) => {
    const videoItem = document.createElement("div");
    videoItem.classList.add("video-item");
    const videoUrl = `https://www.youtube.com/embed/${item.videoId}`;
    const randomTitle = getRandomTitle();

    videoItem.innerHTML = `
      <div class="video-wrapper">
        <iframe src="${videoUrl}" frameborder="0" allowfullscreen></iframe>
        <div class="thumbnail">
          <div class="play-icon">
            <svg viewBox="0 0 24 24">
              <path d="M8 5v14l11-7z"/>
            </svg>
          </div>
        </div>
      </div>
      <div class="video-info">
        
      </div>
    `;

    videoItem.addEventListener("click", function () {
      videoItem.classList.add("playing");
      const iframe = videoItem.querySelector("iframe");
      iframe.src += "?autoplay=1";
    });

    videoGrid.appendChild(videoItem);
  });
});
</script>
