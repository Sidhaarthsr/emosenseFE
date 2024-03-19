<template>
    <div class="container">
      <div class="video-content-container">
        <div class="video-container" ref="videoContainer">
          <h1 class="fade-in">Emosense Adaptive UI</h1>
          <video controls autoplay muted ref="videoPlayer" class="fade-in">
            <source :src="videoSource" type="video/mp4">
            Your browser does not support the video tag.
          </video>
        </div>
        <div class="content" ref="content">
          <h2 class="fade-in delay">About Emosense</h2>
          <p class="fade-in delay">
            Emosense is an innovative project that aims to develop adaptive user interfaces using emotion recognition technology.
            By analyzing user emotions in real-time, Emosense can dynamically adjust UI elements to enhance user experience and engagement.
          </p>
          <!-- Add more content here -->
        </div>
      </div>
      <div class="images-container">
        <img src="@/assets/Image1.jpeg" alt="Image 1" class="fade-in delay">
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        videoSource: require("@/assets/1.mp4") // Update the path to your video file
      };
    },
    mounted() {
      // Listen for the loadedmetadata event to start animations after video loaded
      this.$refs.videoPlayer.addEventListener("loadedmetadata", this.startAnimations);
    },
    beforeDestroy() {
      this.$refs.videoPlayer.removeEventListener("loadedmetadata", this.startAnimations);
    },
    methods: {
      startAnimations() {
        // Add class to trigger fade-in animations when the video is loaded
        this.$refs.videoContainer.classList.add("fade-in");
        
        // Delay animation for the description after 5 seconds
        setTimeout(() => {
          this.$refs.content.classList.add("fade-in");
        }, 5000);
      }
    }
  };
  </script>
  
  <style scoped>
  .container {
    display: flex;
    flex-direction: column;
    align-items: center;
    max-width: 1080px;
    margin: 0 auto;
    padding: 20px;
  }
  
  .video-content-container {
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    width: 100%;
    max-width: 1000px;
    margin-bottom: 20px;
  }
  
  .video-container {
    width: 48%;
  }
  
  .video-container video {
    width: 100%;
    height: auto;
  }
  
  .content {
    width: 48%;
  }
  
  .images-container {
    display: flex;
    justify-content: center;
    margin-bottom: 20px;
  }
  
  .images-container img {
    max-width: 400px;
    margin: 0 10px;
  }
  
  .logo-container img {
    max-width: 200px;
  }
  
  .fade-in {
    animation: fadeInAnimation 1s ease-in forwards;
    opacity: 0;
  }
  
  .delay {
    animation-delay: 5s;
  }
  
  @keyframes fadeInAnimation {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  </style>
  