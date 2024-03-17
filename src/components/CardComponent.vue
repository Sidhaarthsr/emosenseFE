<template>
    <div class="card">
        <div class="card-top" :style="{ backgroundColor: color }"></div> <!-- Apply background color dynamically -->
      <div class="card-header">{{ header }}</div>
      <div class="card-body">
        <p>{{ body }}</p>
      </div>
      <div class="card-footer">{{ footer }}</div>
    </div>
  </template>

  
  <script>
  export default {
    name: 'CardComponent',
    props: {
      header: {
        type: String,
        default: 'Default Header'
      },
      body: {
        type: String,
        default: 'Default Body'
      },
      footer: {
        type: String,
        default: 'Default Footer'
      },
      color: {
      type: String,
      default: 'red' // Default color if not provided
    }
    }
  }
  </script>
  
  <style scoped>
  .card {
    border: 1px solid #ccc;
    border-radius: 8px;
    padding: 16px;
    margin-bottom: 16px;
  }
  
  .card-header {
    font-size: 20px;
    font-weight: bold;
    margin-bottom: 12px;
  }
  
  .card-body {
    margin-bottom: 12px;
  }

  .card-top{
    height: 25px; /* Adjust height as needed */
  border-radius: 8px 8px 0 0; /* Apply border-radius to top corners */
  }
  
  .card-footer {
    font-style: italic;
    color: #666;
  }
  </style>
  