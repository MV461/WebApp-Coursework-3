<template>
  <div id="app">
    <div class="row">
      <!-- Navbar START -->
      <nav class="navbar navbar-expand-md bg-body-tertiary p-2">
        <div class="container-fluid">

          <!-- NavBar Brand START -->
          <span class="navbar-brand mb-0 h1">Lesson Shop</span>
          <!-- NavBar Brand END -->

          <!-- Hamburger Menu Button START -->
          <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav">
            <span class="navbar-toggler-icon"></span>
          </button>
          <!-- Hamburger Menu Button END -->

          <!-- Nav Links START -->
          <div class="collapse navbar-collapse" id="navbarNav">

            <!-- Lessons NavLink START -->
            <div class="navbar-nav mx-auto p-2">
              <button @click="showCart = false" class="btn btn-outline-primary">Lessons</button>
            </div>
            <!-- Lessons NavLink END -->

            <!-- Cart NavLink START -->
            <div class="navbar-nav p-2">
              <button :disabled="isCartEmpty" @click="showCart = !showCart" class="btn btn-outline-danger">Cart</button>
            </div>
            <!-- Cart NavLink END -->

          </div>
          <!-- Nav Links END -->

        </div>
      </nav>
      <!-- Navbar END -->
    </div>

    <!-- Cart Page START -->
    <div v-if="showCart">
      <Checkout :showCart="showCart" :isCartEmpty="isCartEmpty" :lessons="orderedLessons" :cart="cart"
        @remove-item-from-cart="removeFromCart" @add-item-to-cart="addToCart" @show-cart-false="showCartToFalse" />
    </div>
    <!-- Cart Page END -->

    <!-- Lessons Page START -->
    <div v-else>
      <div class="row p-2 pt-4">
        <!-- Sorting/Ordering Col START -->
        <div class="col-md-3">
          <!-- Sorting/Ordering Section START -->
          <aside class="h-100 mb-5">

            <!-- Sorting Section START -->
            <div class="border p-3">
              <h2>Sort By:</h2>

              <!-- Subject Sort Button START -->
              <input type="radio" class="btn-check" name="sort-group" id="subject" value="subject" v-model="sortOption">
              <label class="btn" for="subject">Subject</label>
              <!-- Subject Sort Button END -->

              <!-- Price Sort Button START -->
              <input type="radio" class="btn-check" name="sort-group" id="price" value="price" v-model="sortOption">
              <label class="btn" for="price">Price</label>
              <!-- Price Sort Button END -->

              <!-- Location Sort Button START -->
              <input type="radio" class="btn-check" name="sort-group" id="location" value="location"
                v-model="sortOption">
              <label class="btn" for="location">Location</label>
              <!-- Location Sort Button END -->

              <!-- Spaces Sort Button START -->
              <input type="radio" class="btn-check" name="sort-group" id="spaces" value="spaces" v-model="sortOption">
              <label class="btn" for="spaces">Spaces</label>
              <!-- Spaces Sort Button END -->

            </div>
            <!-- Sorting Section END -->

            <!-- Ordering Section START -->
            <div class="border p-3">
              <h2>Order By:</h2>

              <!-- Ascending Order Button START -->
              <input type="radio" class="btn-check" name="order-group" id="ascending" value="ascending"
                v-model="orderOption">
              <label class="btn" for="ascending">Ascending</label>
              <!-- Ascending Order Button END -->

              <!-- Descending Order Button START -->
              <input type="radio" class="btn-check" name="order-group" id="descending" value="descending"
                v-model="orderOption">
              <label class="btn" for="descending">Descending</label>
              <!-- Descending Order Button END -->

            </div>
            <!-- Ordering Section END -->

          </aside>
          <!-- Sorting/Ordering Column END -->
        </div>
        <!-- Sorting/Ordering Col END -->

        <!-- Card/Search Bar Col START -->
        <div class="col-md-9">
          <main>
            <!-- Search Bar START -->
            <input type="text" class="form-control form-control-lg mb-3" placeholder="Search Lessons..."
              v-model="search">
            <!-- Search Bar END -->

            <Lesson :lessons="orderedLessons" :cart="cart" @add-item-to-cart="addToCart"
              @remove-item-from-cart="removeFromCart" />


          </main>
        </div>
        <!-- Card/Search Bar Col END -->
      </div>
    </div>
    <!-- Lessons Page END -->

  </div>
</template>

<script>
// import MyComponent from './components/DummyBoot.vue';
import Lesson from './components/Lesson.vue';
import Checkout from './components/Checkout.vue';

export default {
  name: 'App',
  components: {
    Lesson,
    Checkout
  },
  data() {
    return {
      // Initializing data properties
      lessons: [], // Holds lesson data retrieved from lessons.json
      cart: {}, // Keeps track of items in the cart
      // formData: { // Form data for customer details
      //   name: "",
      //   number: ""
      // },
      showCart: false, // Tracks whether to display the cart
      search: "", // Search query for filtering lessons
      sortOption: "price", // Default sorting option for lessons
      orderOption: "ascending" // Default order for sorting (ascending/descending)
    }
  },
  created() {
    // Fetching lesson data when the Vue instance is created
    // Initializing 'lessons' data property with fetched data
    fetch('lessons.json')
      .then(response => {
        if (!response.ok) {
          throw new Error('Network response was not ok');
        }
        return response.json();
      })
      .then(data => {
        this.lessons = data;
      })
      .catch(error => {
        console.error('Error loading JSON data', error);
      });
  },
  methods: {
    // Add a lesson to the cart
    addToCart(lessonID) {
      // Retrieve necessary data
      const CART = this.cart;
      const QUANTITY_IN_CART = CART[lessonID];

      // Set a const for null (for readability)
      const ZERO_QUANTITY = null;

      // Calculate new quantity to add to cart
      const NEW_QUANTITY = ((QUANTITY_IN_CART == ZERO_QUANTITY) ? (1) : (QUANTITY_IN_CART + 1));

      // Find the lesson being added and decrement available spaces
      const ADDED_LESSON = this.lessons.find((lesson) => lesson.id === lessonID);


      // Update cart with new quantity and adjust lesson spaces
      // this.$set(CART, lessonID, NEW_QUANTITY);
      CART[lessonID] = NEW_QUANTITY; // Directly assign the new quantity
      // Decrease available spaces for the lesson
      ADDED_LESSON.spaces--;
    },

    // Remove a lesson from the cart
    removeFromCart(lessonID) {
      // Retrieve necessary data
      const CART = this.cart;
      const QUANTITY_IN_CART = CART[lessonID];

      // Calculate reduced quantity and find the lesson
      const REDUCED_QUANTITY = QUANTITY_IN_CART - 1;
      const ADDED_LESSON = this.lessons.find((lesson) => lesson.id === lessonID);


      // Update cart with reduced quantity and adjust lesson spaces
      // this.$set(CART, lessonID, REDUCED_QUANTITY);
      CART[lessonID] = REDUCED_QUANTITY; // Directly assign the reduced quantity

      // If quantity becomes zero, remove lesson from cart
      if (REDUCED_QUANTITY === 0) {
        // this.$delete(CART, lessonID);
        delete CART[lessonID];
      }

      // Increase available spaces for the lesson
      ADDED_LESSON.spaces++;
    },

    showCartToFalse() {
      this.showCart = false;
    }
  },
  computed: {
    // Filter lessons based on the search query
    filteredLessons() {
      console.log("filtered triggered!")
      if (!this.lessons) {
        return []; // Return an empty array if lessons is null
      }
      // Gather all the necessary data
      const LESSONS = this.lessons;
      const CATEGORIES_TO_SEARCH = ['subject', 'location', 'price', 'spaces'];
      const QUERY = this.search.toLowerCase();

      // Return filtered lessons
      return LESSONS.filter(
        // For each lesson...
        (lesson) => CATEGORIES_TO_SEARCH.some(
          // ...and for each of its attributes...
          (attribute) => {
            // ...take its value, format it...
            const ATTRIBUTE_VALUE = lesson[attribute];
            const FORMATTED_ATTRIBUTE_VALUE = ATTRIBUTE_VALUE.toString().toLowerCase();

            // ...and check whether it includes the QUERY
            return FORMATTED_ATTRIBUTE_VALUE.includes(QUERY)
          }
        )
      )
    },

    // Sort lessons based on selected option
    sortedLessons() {
      console.log("sorted triggered!")

      // Gather all the necessary data
      const FILTERED_LESSONS = this.filteredLessons;
      const ATTRIBUTE_TO_SORT_BY = this.sortOption;
      // All functions below sort in ascending
      const SORT_FUNCTION_SELECTOR = {
        'subject': (lesson1, lesson2) => lesson1.subject.localeCompare(lesson2.subject),
        'price': (lesson1, lesson2) => lesson1.price - lesson2.price,
        'location': (lesson1, lesson2) => lesson1.location.localeCompare(lesson2.location),
        'spaces': (lesson1, lesson2) => lesson1.spaces - lesson2.spaces
      };

      // Return sorted filtered lessons based on ATTRIBUTE_TO_SORT_BY
      return FILTERED_LESSONS.sort(SORT_FUNCTION_SELECTOR[ATTRIBUTE_TO_SORT_BY]);
    },

    // Order lessons based on selected option (ascending/descending)
    orderedLessons() {
      console.log("ordered triggered!")

      // Gather necessary data
      // Creating a copy, as .reverse() changes array in-place
      const SORTED_LESSONS = this.sortedLessons.slice();
      const ORDER_BY = this.orderOption;

      // if we need descending reverse filtered sorted lessons...
      if (ORDER_BY === 'descending') {
        return SORTED_LESSONS.reverse();
      } else {
        // ...else just return the original filtered sorted lessons.
        return SORTED_LESSONS;
      }
    },

    // Check if the cart is empty
    isCartEmpty() {
      console.log("Cart empty?" + !Object.keys(this.cart).length)
      return !Object.keys(this.cart).length;
    }
  }
};

</script>

<style scoped>
/* CSS for Lessons buttin on NavBar */
#navbarNav .nav-link:hover {
  color: white;
  background-color: blue;
}

/* CSS for Cart button on NavBar */
#navbarNav #cart:hover {
  color: white;
  background-color: red;
}
</style>