<template>
    <div class="container">
      <div class="video-content-container fade-in">
        <div class="video-container" ref="videoContainer">
          <video autoplay muted loop ref="videoPlayer">
            <source :src="videoSource" type="video/mp4">
            Your browser does not support the video tag.
          </video>
        </div>
        <div class="images-container">
          <div class="image-placeholder">
            <img
              src="@/assets/Image1.jpeg"
              alt="Image 1"
              class="image"
              @mouseover="showTooltip('Image 1')"
              @mouseleave="hideTooltip"
            />
            <img
              src="@/assets/Image2.jpeg"
              alt="Image 2"
              class="image"
              @mouseover="showTooltip('Image 2')"
              @mouseleave="hideTooltip"
            />
          </div>
        </div>
      </div>
      <div class="content fade-in">
        <h2 class="title">About Emosense</h2>
        <p class="description">
          Emosense is an innovative project that aims to develop adaptive user interfaces using emotion recognition technology.
          By analyzing user emotions in real-time, Emosense can dynamically adjust UI elements to enhance user experience and engagement.
        </p>
        <p class="description">
          With Emosense, applications can respond to user emotions, providing personalized experiences and improving overall satisfaction.
        </p>
      </div>
      <div class="tooltip" v-if="showTooltipFlag">{{ tooltipText }}</div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        videoSource: require("@/assets/1.mp4"),
        showTooltipFlag: false,
        tooltipText: ""
      };
    },
    methods: {
      showTooltip(text) {
        this.showTooltipFlag = true;
        this.tooltipText = text;
      },
      hideTooltip() {
        this.showTooltipFlag = false;
        this.tooltipText = "";
      }
    },
    mounted() {
      this.$refs.videoPlayer.addEventListener("loadedmetadata", this.startAnimations);
    },
    beforeDestroy() {
      this.$refs.videoPlayer.removeEventListener("loadedmetadata", this.startAnimations);
    },
    startAnimations() {
      this.$refs.videoContainer.classList.add("fade-in");
    }
  };
  </script>
  
  <style scoped>
  .container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
  }
  
  .video-content-container {
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    margin-bottom: 40px;
  }
  
  .video-container {
    width: 65%;
  }
  
  .video-container video {
    width: 100%;
    height: 40vh;
  }
  
  .images-container {
    display: flex;
    justify-content: flex-end;
    align-items: flex-start;
  }
  
  .image-placeholder {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  
  .image {
    max-width: 300px;
    height: 150px;
    margin-bottom: 20px;
    cursor: pointer;
    border-radius: 10px;
    box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
    transition: transform 0.3s ease;
  }
  
  .image:last-child {
    margin-bottom: 0;
  }
  
  .image:hover {
    transform: translateY(-5px);
  }
  
  .content {
    text-align: center;
    opacity: 0;
  }
  
  .fade-in {
    animation: fadeInAnimation 1s ease-in forwards;
  }
  
  @keyframes fadeInAnimation {
    from {
      opacity: 0;
    }
    to {
      opacity: 1;
    }
  }
  
  .title {
    font-size: 2rem;
    margin-bottom: 15px;
    color: #333;
  }
  
  .description {
    font-size: 1rem;
    line-height: 1.6;
    color: #666;
  }
  
  .tooltip {
    position: absolute;
    background-color: rgba(0, 0, 0, 0.8);
    color: #fff;
    padding: 5px 10px;
    border-radius: 5px;
    font-size: 14px;
    transition: opacity 0.3s ease;
    opacity: 0;
  }
  
  .tooltip.active {
    opacity: 1;
  }
  </style>
  