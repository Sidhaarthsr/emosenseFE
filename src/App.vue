<template>
  <div id="app">
    <header class="header">
      <div class="logo-container">
        <img src="@/assets/logo.jpeg" alt="Logo" class="logo"> <!-- Add your logo here -->
      </div>
      <div class="header-content">
        <h1 class="header-title">Adaptive UI</h1>
      </div>
    </header>
    <HomePage v-if="showHomePage"></HomePage>
    <div v-else>
      <div class="card-row">
        <CardComponent
          v-for="(card, index) in cardData.slice(0, 3)"
          :key="index"
          :header="card.header"
          :body="card.body"
          :footer="card.footer"
          :color="card.color"
          :fontStyle="card.fontStyle"
          class="card"
        ></CardComponent>
      </div>
      <div class="card-row">
        <CardComponent
          v-for="(card, index) in cardData.slice(3, 6)"
          :key="index"
          :header="card.header"
          :body="card.body"
          :footer="card.footer"
          :color="card.color"
          :fontStyle="card.fontStyle"
          class="card"
        ></CardComponent>
      </div>
    </div>
    <button @click="toggleView" class="toggle-button">{{ showHomePage ? 'Next' : 'Back to Home' }}</button>
    <footer class="footer">
      <p class="footer-text">© 2024 Adaptive UI. All rights reserved.</p>
    </footer>
  </div>
</template>
<script>
import CardComponent from './components/CardComponent.vue';
import HomePage from './components/HomePage.vue';
import 'bootstrap/dist/css/bootstrap.css';

export default {
  components: {
    CardComponent,
    HomePage,
  },
  name: 'App',
  data() {
    return {
      originalColor: '#FF0000',
      showHomePage: true,
      colors: {
        card1: '#ff0000',  // Red
        card2: '#ffff00',  // Yellow
        card3: '#0000ff',  // Blue
        card4: '#008000',  // Green
        card5: '#a52a2a',  // Brown
        card6: '#800080'   // Purple
    },
      cardData: [],
    }
  },
  computed: {
    cardRows() {
      const rows = [];
      for (let i = 0; i < this.cardData.length; i += 3) {
        rows.push(this.cardData.slice(i, i + 3));
      }
      return rows;
    }
  },
  methods: {
    toggleView() {

      this.showHomePage = !this.showHomePage;
      if(this.showHomePage===false)
      {
        this.startCamera();
      }
      else
      {
        this.stopCamera();
      }
    },
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
             
              this.cardData.forEach((card, index) => {
                const originalRgb = this.hexToRgb(card.color);
                console.log("Original Color",card.color);

                const adjustedColor = this.adjustColorByLuminance(originalRgb, -2);
                console.log("Adjusted Color",adjustedColor)
                this.$set(this.cardData, index, { ...card, color: adjustedColor });
              });
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
   this.cardData = [
      {
        header: "First Card",
        body: "Once upon a time, there was a little mouse named Jerry who lived in a cozy burrow beneath the old oak tree. He loved exploring the forest and making friends with other creatures.",
        footer: "The end of the first card",
        color: this.originalColor,
      },
      {
        header: "Second Card",
        body: "In a distant kingdom, there was a brave knight named Sir Arthur. He embarked on a quest to rescue the princess from the clutches of an evil sorcerer who dwelled in the dark castle atop the hill.",
        footer: "The end of the second card",
        color: this.colors.card2,
      },
      {
        header: "Third Card",
        body: "Deep in the heart of the Amazon rainforest, a group of explorers stumbled upon a hidden temple filled with ancient treasures and mysterious artifacts.",
        footer: "The end of the third card",
        color: this.colors.card3,
      },
      {
        header: "Fourth Card",
        body: "On a sunny morning, a young boy named Timmy found a mysterious map in his attic. It led him on an adventure across the seven seas in search of buried treasure.",
        footer: "The end of the fourth card",
        color: this.colors.card4,
      },
      {
        header: "Fifth Card",
        body: "In the bustling city of New York, a struggling artist named Emily stumbled upon an enchanted paintbrush that brought her vivid imagination to life.",
        footer: "The end of the fifth card",
        color: this.colors.card5,
      },
      {
        header: "Sixth Card",
        body: "In the quaint village of Willowbrook, a mischievous gnome named Grumbletoes caused chaos with his pranks until he learned the true meaning of friendship.",
        footer: "The end of the sixth card",
        color: this.colors.card6,
      }
    ];

  }
}
</script>

<style lang="scss">


body {
  margin: 0;
  padding: 0;
  font-family: 'Segoe UI', Tahoma;
  font-size: 1rem;
  background-color: #f5f5f5; /* Set a light background color */
}

#app {
  display: flex;
  flex-direction: column;
  align-items: center;
}



.card-row {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.card {
  width: calc(33.33% - 10px);
  box-sizing: border-box;
  background-color: #fff; /* White card background */
  border-radius: 10px; /* Rounded corners */
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1); /* Shadow effect */
  padding: 20px;
  transition: transform 0.3s ease; /* Smooth transition on hover */
}

.card:hover {
  transform: translateY(-5px); /* Lift card on hover */
}

.toggle-button {
  background-color: #4287f5; /* Blue button color */
  color: #fff; /* White text color */
  border: none;
  border-radius: 5px;
  padding: 10px 20px;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s ease; /* Smooth transition on hover */
}

.toggle-button:hover {
  background-color: #3366cc; /* Darker blue on hover */
}


.header {
  background: linear-gradient(135deg, #4287f5, #ff9e15); /* Gradient background */
  color: #fff; /* White text color */
  padding: 10px; /* Reduced padding */
  display: flex;
  justify-content: space-between; /* Distribute items evenly */
  align-items: center; /* Center items vertically */
  width: 100%; /* Ensure header spans full width */
}

.header-title {
  margin: 0;
  font-size: 1.5rem; /* Reduced font size */
  font-weight: bold; /* Bold font weight */
  letter-spacing: 1px; /* Reduced letter spacing */
  text-align: center; /* Center title */
  flex-grow: 1; /* Allow title to grow and occupy remaining space */
}


.header-content {
  display: flex;
  align-items: center;
  flex-grow: 1; /* Allow it to take up remaining space */
  justify-content: center; /* Center horizontally */
}

.logo-container {
  display: flex; /* Make the container a flexbox */
  align-items: center; /* Center items vertically */
}

.logo {
  width: 100px; /* Adjust the width of the logo */
  height: auto; /* Maintain aspect ratio */
}
.footer {
  color: #fff; /* White text color */
  padding: 10px; /* Reduced padding */
  display: flex;
  justify-content: center; /* Center content horizontally */
  align-items: center; /* Center content vertically */
  width: 100%; /* Ensure footer spans full width */
}

.footer-text {
  margin: 0;
  color: black;
  font-size: 0.8rem; /* Reduced font size */
  text-align: right; /* Center text */
}
.header {
  margin-bottom: 0; /* Remove bottom margin */
}

.footer {
  margin-top: 0; /* Remove top margin */
}


</style>
