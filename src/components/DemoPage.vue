<template>
    <div id="demo">
      <CardComponent
        header="First Card"
        body="Once upon a time, there was a little mouse named Jerry who lived in a cozy burrow beneath the old oak tree. He loved exploring the forest and making friends with other creatures."
        footer="The end of the first card"
        :color="originalColor"
        :fontStyle="'Arial, sans-serif'" 
      ></CardComponent>
      <CardComponent
        header="Second Card"
        body="In a distant kingdom, there was a brave knight named Sir Arthur. He embarked on a quest to rescue the princess from the clutches of an evil sorcerer who dwelled in the dark castle atop the hill."
        footer="The end of the second card"
        color="yellow"
        :fontStyle="'Times New Roman, serif'"
      ></CardComponent>
      <CardComponent
        header="Third Card"
        body="Deep in the heart of the Amazon rainforest, a group of explorers stumbled upon a hidden temple filled with ancient treasures and mysterious artifacts."
        footer="The end of the third card"
        color="blue"
        :fontStyle="'Verdana, Geneva, sans-serif'" 
      ></CardComponent>
      <CardComponent
        header="Fourth Card"
        body="On a sunny morning, a young boy named Timmy found a mysterious map in his attic. It led him on an adventure across the seven seas in search of buried treasure."
        footer="The end of the fourth card"
        color="purple"
      ></CardComponent>
      <CardComponent
        header="Fifth Card"
        body="In the bustling city of New York, a struggling artist named Emily stumbled upon an enchanted paintbrush that brought her vivid imagination to life."
        footer="The end of the fifth card"
        color="orange"
      ></CardComponent>
      <CardComponent
        header="Sixth Card"
        body="In the quaint village of Willowbrook, a mischievous gnome named Grumbletoes caused chaos with his pranks until he learned the true meaning of friendship."
        footer="The end of the sixth card"
        color="green"
      ></CardComponent>
      <CardComponent
        header="Seventh Card"
        body="In a land far, far away, a young dragon named Sparky struggled to control his fiery breath until he discovered the power of meditation."
        footer="The end of the seventh card"
        color="brown"
      ></CardComponent>
      <CardComponent
        header="Eighth Card"
        body="Amidst the snowy peaks of the Himalayas, a yeti named Yuki embarked on a journey to find his long-lost family and unlock the secrets of his past."
        footer="The end of the eighth card"
        color="cyan"
      ></CardComponent>
      <CardComponent
        header="Ninth Card"
        body="In the magical kingdom of Avalon, a humble wizard named Merlin mentored a young sorceress named Morgana, unaware of her dark destiny."
        footer="The end of the ninth card"
        color="maroon"
      ></CardComponent>
      
    </div>
  </template>
  
  
  
  <script>
  
  export default {

    data() {
      return {
        originalColor :'#00FF00',
        width : 320,   // We will scale the photo width to this
        height : 0,   // This will be computed based on the input stream
        intervalId: null // New property to hold the interval ID
      }
    },
    methods: {  
      get:function(){
      this.$http.get('https://jsonplaceholder.typicode.com/todos/1')
      .then(response => {
        // Handle the response data
        console.log(response.body);
      })
      .catch(error => {
        // Handle errors
        console.error('Error:', error);
      });
      },  
      captureImage() {
      // Create a new video element
      const videoElement = document.createElement('video');
    
      // Request access to the camera
      navigator.mediaDevices.getUserMedia({ video: true })
      .then(stream => {
        videoElement.srcObject = stream;
        videoElement.onloadedmetadata = () => {
          // Once the metadata is loaded, set the canvas dimensions
          const canvas = document.createElement('canvas');
          const ctx = canvas.getContext('2d');
          canvas.width = videoElement.videoWidth;
          canvas.height = videoElement.videoHeight;
          
          // Draw the video frame onto the canvas
          ctx.drawImage(videoElement, 0, 0, canvas.width, canvas.height);
          
          // Convert the canvas image to a data URL
          const imageDataUrl = canvas.toDataURL('image/jpeg');
          
          // Save the image to the filesystem
          this.saveImage(imageDataUrl);
          
          // Stop the video stream to release resources
          stream.getTracks().forEach(track => {
            track.stop();
          });
        };
      })
      .catch(error => {
        console.error('Error accessing camera:', error);
      });
      },
      hexToRgb(hex) {
      // Remove '#' if present
      hex = hex.replace(/^#/, '');
  
      // Parse hexadecimal components
      const r = parseInt(hex.substring(0, 2), 16);
      const g = parseInt(hex.substring(2, 4), 16);
      const b = parseInt(hex.substring(4, 6), 16);
  
      return [r, g, b]; // Return RGB array
      },
  adjustColorByLuminance(color, arbitraryValue) {
    const luminance = this.calculateLuminance(color);
      let newLuminance;
      if (arbitraryValue >= 0) {
          newLuminance = luminance * (1 + arbitraryValue * 0.1); // Increase luminance for positive values
      } else {
          newLuminance = luminance / (1 - arbitraryValue * 0.1); // Decrease luminance for negative values
      }
      
      // Modify the color based on the new luminance
      const r = Math.round(color[0] * newLuminance / luminance);
      const g = Math.round(color[1] * newLuminance / luminance);
      const b = Math.round(color[2] * newLuminance / luminance);
  
      // Clip the RGB values to ensure they are within the valid range [0, 255]
      const adjustedR = Math.max(0, Math.min(r, 255));
      const adjustedG = Math.max(0, Math.min(g, 255));
      const adjustedB = Math.max(0, Math.min(b, 255));
  
      // Convert RGB values to hexadecimal format
      const hex = "#" + ((1 << 24) + (adjustedR << 16) + (adjustedG << 8) + adjustedB).toString(16).slice(1);
      
      return hex;
    
  },
  calculateLuminance(color) {
      const r = color[0] / 255;
      const g = color[1] / 255;
      const b = color[2] / 255;
      return 0.2126 * r + 0.7152 * g + 0.0722 * b;
  },
      saveImage(imageDataUrl) {
  
         // Convert the base64 data to a Blob object
    const byteCharacters = atob(imageDataUrl.split(',')[1]);
    const byteNumbers = new Array(byteCharacters.length);
    for (let i = 0; i < byteCharacters.length; i++) {
      byteNumbers[i] = byteCharacters.charCodeAt(i);
    }
    const byteArray = new Uint8Array(byteNumbers);
    const blob = new Blob([byteArray], { type: 'image/jpeg' });
  
    // Create a temporary download link
    const link = document.createElement('a');
    link.href = URL.createObjectURL(blob);
    link.download = './assests/captured_image.jpg';
  
    // Trigger a click event on the link to initiate the download
    link.click();
  
        
      },
      takePhoto() {
        navigator.mediaDevices.getUserMedia({ video: true })
          .then(stream => {
            const videoElement = document.createElement('video');
            videoElement.srcObject = stream;
            videoElement.onloadedmetadata = () => {
              // Once the metadata is loaded, capture the image
              this.captureImage(videoElement);
            };
          })
          .catch(error => {
            console.error('Error accessing camera:', error);
          });
      },
      startCamera() {
        navigator.mediaDevices.getUserMedia({ video: true })
          .then(stream => {
            const videoElement = document.createElement('video');
            videoElement.srcObject = stream;
            videoElement.onloadedmetadata = () => {
              // Once the metadata is loaded, capture the image
              this.captureImage(videoElement);
              // Start capturing images at intervals
              this.intervalId = setInterval(() => {
                this.captureImage(videoElement);
                const originalRgb = this.hexToRgb(this.originalColor); // Convert hexadecimal to RGB array
                this.originalColor = this.adjustColorByLuminance(originalRgb, -2); // Adjust color by luminance
  
               console.log(this.originalColor);
              }, 10000); // Capture every 10 seconds
            };
          })
          .catch(error => {
            console.error('Error accessing camera:', error);
          });
      },
      stopCamera() {
        // Clear the interval to stop capturing images
        clearInterval(this.intervalId);
      },
    },
    mounted() {
     this.get();
     this.startCamera();
    }
  }
  </script>
  
  <style lang="scss">
  .camera {
    position: relative;
  }
  .camera button {
    position: absolute;
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
  }
  body{
    margin: 0;
    padding: 0;
    font-family: 'Segoe UI', Tahoma;
    font-size: 1rem;
  }
  #app {
    display: flex;
    flex-wrap: wrap; /* Allow cards to wrap to the next row */
    justify-content: space-between; /* Align the cards to the left and right sides */
    align-items: center; /* Center the cards vertically */
    height: 100vh; /* Set the height of the container to 100% of the viewport height */
    padding: 0 20px; /* Add space on the left and right sides */
  }
  
  .card {
    width: calc(33.33% - 20px); /* Adjust the width as per your requirement */
    margin-bottom: 20px;
    box-sizing: border-box; /* Ensure padding and border are included in the width */
    border-radius: 15px; /* Add border-radius for curved edges */
    overflow: hidden; /* Hide overflowing content */
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1); /* Add shadow for depth */
  }
  .textbox{
    padding: 20px;
    width: calc(100% - 40px);
    max-width: 480px;
    border : 1px solid #ccc;
    font-size: 1.2rem;
    color: #333;
    background-color: #efefef;
  
    &.dark{
      color: #efefef;
      background-color: #333;
      font-size: 1rem;
    }
  }
  
  
  </style>
  