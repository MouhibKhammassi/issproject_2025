<template>
    <v-container v-if="car">
      <v-btn color="primary back_btn_margin" class="gradient-button" @click="goBack">⬅ Back</v-btn>
        
      <v-row>
        <!-- ✅ Image Gallery -->
        <v-col cols="12" md="6">
          <v-carousel hide-delimiters show-arrows>
            <v-carousel-item 
              v-for="(image, index) in car.images" 
              :key="index"
              @click="openImage(index)"
            >
              <v-img :src="getImage(image)" class="clickable-image"/>
            </v-carousel-item>
          </v-carousel>
        </v-col>
  
        <!-- ✅ Car Details Section -->
        <v-col cols="12" md="6">
          <h1>{{ car.brand }} {{ car.model }}</h1>
          <p><strong>Year:</strong> {{ car.year }}</p>
          <p><strong>Seats:</strong> {{ car.seats }}</p>
          <p><strong>Convertible:</strong> {{ car.isConvertible ? 'Yes' : 'No' }}</p>
          <p><strong>Price per Day:</strong> ${{ car.pricePerDay }}</p>
          <p><strong>Availability:</strong> {{ car.isAvailable? 'Available' : 'Unavailable' }}</p>
          <p><strong>Location:</strong> {{ car.location }}</p>
  
          <!-- ✅ Book This Car Button -->
          <v-btn 
            :color="car.isAvailable ? 'success' : 'grey'"
            class="book-button gradientB-button"
            :disabled="!car.isAvailable"
            @click="bookCar"
          >
            Book This Car
          </v-btn>
        </v-col>
      </v-row>
  
      <!-- ✅ Description Section -->
      <v-row>
        <v-col cols="12">
          <h2>Description</h2>
          <p>{{ car.description || 'No description available for this car.' }}</p>
        </v-col>
      </v-row>
  
      <!-- ✅ Contact Us Section -->
      <v-row>
        <v-col cols="12">
          <h2>Contact Us</h2>
          <p>If you have any questions about this car, feel free to contact us.</p>
          <v-btn color="info" href="/contact">Contact Us</v-btn>
        </v-col>
      </v-row>
  
      <!-- ✅ Image Zoom Modal with Navigation -->
      <v-dialog v-model="imageDialog" max-width="80%" persistent @click:outside="closeImage">
        <v-card class="zoomed-image-card">
          <!-- Close Button -->
          <v-btn icon class="close-button" @click="closeImage">
            <v-icon>mdi-close</v-icon>
          </v-btn>
  
          <!-- Image Navigation -->
          <v-btn icon class="prev-button" @click="prevImage" v-if="car.images.length > 1">
            <v-icon>mdi-chevron-left</v-icon>
          </v-btn>
          
          <v-img :src="selectedImage" class="zoomed-image"></v-img>
          
          <v-btn icon class="next-button" @click="nextImage" v-if="car.images.length > 1">
            <v-icon>mdi-chevron-right</v-icon>
          </v-btn>
        </v-card>
      </v-dialog>
  
    </v-container>
  
    <!-- ✅ No Car Found -->
    <v-container v-else>
      <h2 class="text-center">Car Not Found</h2>
    </v-container>
  </template>
  
  <script setup>
  import { computed, ref } from 'vue';
  import { useRoute, useRouter } from 'vue-router';
  import cars from '@/assets/data/Cars.js';
  
  const route = useRoute();
  const router = useRouter();
  
  // ✅ Check if user is logged in
  const isLoggedIn = ref(true); // Change this based on your actual auth logic
  
  // ✅ Find the correct car based on the ID from the URL
  const car = computed(() => {
    return cars.find((c) => c.carId === route.params.id);
  });
  
  const goBack = () => {
    router.go(-1);
  };
  
  const bookCar = () => {
    if (!isLoggedIn.value) {
      router.push('/login'); // Redirect to login if not logged in
    } else {
      alert(`Booking confirmed for ${car.value.brand} ${car.value.model}`);
    }
  };
  
  // ✅ Image Zoom Logic
  const imageDialog = ref(false);
  const selectedImage = ref("");
  const currentImageIndex = ref(0);
  
  const openImage = (index) => {
    currentImageIndex.value = index;
    selectedImage.value = getImage(car.value.images[index]);
    imageDialog.value = true;
  };
  
  const closeImage = () => {
    imageDialog.value = false;
  };
  
  const prevImage = () => {
    if (currentImageIndex.value > 0) {
      currentImageIndex.value--;
      selectedImage.value = getImage(car.value.images[currentImageIndex.value]);
    }
  };
  
  const nextImage = () => {
    if (currentImageIndex.value < car.value.images.length - 1) {
      currentImageIndex.value++;
      selectedImage.value = getImage(car.value.images[currentImageIndex.value]);
    }
  };
  
  // ✅ Function to resolve image paths
  const getImage = (path) => {
    return new URL(path.replace("@/", "/src/"), import.meta.url).href;
  };
  </script>
  
  <style scoped>
  /* ✅ CarDetails Page Styles */
  h1 {
    font-size: 2rem;
    font-weight: bold;
    margin-bottom: 10px;
  }
  
  p {
    font-size: 1.2rem;
    margin: 5px 0;
  }
  
  .book-button {
    margin-top: 20px;
    width: 100%;
  }
  
  /* ✅ Disabled Button Styling */
  .v-btn[disabled] {
    opacity: 0.5;
    cursor: not-allowed;
  }
  
  /* ✅ Gradient Button */
  .gradient-button {
    background: linear-gradient(135deg, #1c1c1c, #1c1c1c);
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 8px;
    transition: all 0.3s ease;
  }
  
  .gradient-button:hover {
    background: linear-gradient(135deg, #1166c8, #1212ca);
    box-shadow: 0px 0px 10px rgba(58, 29, 248, 0.8);
    transform: scale(1.05);
  }

  /* ✅ Gradient Button for booking */
  .gradientB-button {
    background: linear-gradient(135deg, #1c1c1c, #1c1c1c);
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 8px;
    transition: all 0.3s ease;
  }
  
  .gradientB-button:hover {
    background: linear-gradient(135deg, #1ee60c, #07c71e);
    box-shadow: 0px 0px 10px rgba(6, 171, 6, 0.8);
    transform: scale(1.05);
  }
  
  /* ✅ Clickable Image */
  .clickable-image {
    cursor: pointer;
    transition: transform 0.3s ease;
  }
  
  
  
  /* ✅ Image Zoom Modal */
  .zoomed-image-card {
    background: rgba(0, 0, 0, 0.9);
    padding: 20px;
    border-radius: 10px;
    position: relative;
    text-align: center;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  
  /* ✅ Zoomed Image */
  .zoomed-image {
    width: 80%;
    height: auto;
    max-height: 80vh;
    border-radius: 10px;
  }
  
  /* ✅ Close Button */
  .close-button {
    position: absolute;
    top: 10px;
    right: 10px;
    color: white;
  }
  
  /* ✅ Navigation Buttons */
  .prev-button,
  .next-button {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    color: white;
    font-size: 30px;
    background: rgba(0, 0, 0, 0.5);
    border-radius: 50%;
  }
  
  .prev-button {
    left: 10px;
  }
  
  .next-button {
    right: 10px;
  }
  .back_btn_margin {
  margin-bottom: 25px; 
}
  </style>
  