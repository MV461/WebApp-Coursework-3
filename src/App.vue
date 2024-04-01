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
      <div class="col p-3">
        <!-- Cart Table Row START -->
        <div class="row">
          <!-- Cart Table START -->
          <h1>Cart Details</h1>
          <table class="table table-bordered">
            <thead>
              <!-- Cart Table Column Headers START -->
              <tr>
                <th scope="col">Subject</th>
                <th scope="col">Price</th>
                <th scope="col">Quantity</th>
                <th scope="col">Subtotal</th>
              </tr>
              <!-- Cart Table Column Headers END -->
            </thead>
            <tbody>
              <!-- Cart Table Entry Insertion Loop START -->
              <tr v-for="cartTableEntry in cartTableContents" :key="cartTableEntry.lesson.id">

                <td>
                  <i :class="cartTableEntry.lesson.icon.replace('fa-4x', '')"></i>
                  {{ cartTableEntry.lesson.subject }}
                </td>
                <td>{{ cartTableEntry.lesson.price }} AED</td>
                <td class="d-flex">
                  <span class="me-auto">
                    {{ cartTableEntry.quantity }}
                  </span>

                  <!-- Cart Entry Quantity Increase Button START -->
                  <button class="btn btn-outline-secondary m-1" v-if="cartTableEntry.lesson.spaces > 0"
                    @click="addToCart(cartTableEntry.lesson.id)">
                    <i class="bi bi-plus"></i>
                  </button>
                  <!-- Cart Entry Quantity Increase Button END -->

                  <!-- Cart Entry Quantity Decrease Button START -->
                  <button class="btn btn-outline-secondary m-1" @click="removeFromCart(cartTableEntry.lesson.id)">
                    <i class="bi bi-dash"></i>
                  </button>
                  <!-- Cart Entry Quantity Decrease Button END -->

                </td>
                <td>{{ cartTableEntry.subtotal }} AED</td>
              </tr>
              <!-- Cart Table Entry Insertion Loop END -->
            </tbody>
            <tfoot class="table-group-divider">
              <tr>
                <th colspan="3" scope="row">Total:</th>
                <td><b>{{ cartPrice }} AED</b></td>
              </tr>
            </tfoot>

          </table>
          <!-- Cart Table END -->
        </div>
        <!-- Cart Table Row END -->

        <!-- Order Form Row START -->
        <div class="row p-2 border">
          <!-- Order Form START -->
          <h1>Order Form</h1>
          <form @submit.prevent="orderFormSubmit">

            <!-- Name Input START -->
            <div class="mb-3">
              <label for="name-input" class="form-label">Name</label>
              <input @input="restrictNameInput" v-model="formData.name" placeholder="Enter Name" type="text"
                class="form-control" id="name-input">
            </div>
            <!-- Name Input END -->

            <!-- Phone Number Input START -->
            <div class="mb-3">
              <label for="phoneNumber-input" class="form-label">Phone Number</label>
              <input @input="restrictPhoneNumberInput" v-model="formData.number" placeholder="Enter Phone Number"
                type="text" class="form-control" id="phoneNumber-input">
            </div>
            <!-- Phone Number Input END -->

            <!-- Submit Button START -->
            <button :disabled="!(formData.name && formData.number)" type="submit" class="btn btn-primary">
              Submit
            </button>
            <!-- Submit Button END -->
          </form>
          <!-- Order Form END -->
        </div>
        <!-- Order Form Row END -->

      </div>
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

            <Lesson :lessons="orderedLessons" :cart="cart" @add-item-to-cart="addToCart" @remove-item-from-cart="removeFromCart" />


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

export default {
  name: 'App',
  components: {
    Lesson
  },
  data() {
    return {
      // Initializing data properties
      lessons: [], // Holds lesson data retrieved from lessons.json
      cart: {}, // Keeps track of items in the cart
      formData: { // Form data for customer details
        name: "",
        number: ""
      },
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

    // Process the order form submission
    orderFormSubmit() {
      // Gather necessary data for the order
      const CUSTOMER_DETAIL = this.formData;
      const CART_ITEMS = this.cart;
      const TOTAL_PRICE = this.cartPrice;

      // Create order object and stringify for display
      const ORDER = {
        formData: CUSTOMER_DETAIL,
        cart: CART_ITEMS,
        total: TOTAL_PRICE
      };
      const ORDER_STRING = JSON.stringify(ORDER, null, 2);

      // Set CURRENT_PAGE const
      const CURRENT_PAGE = window.location;

      // Generates order confirmation alert.
      alert(`Order Placed!\n\n${ORDER_STRING}`);
      // Reload current page
      CURRENT_PAGE.reload();
    },

    // Restrict input in the 'name' field to alphabetic characters
    restrictNameInput(nameFieldInput) {
      // Get the form object
      const FORM_DATA = this.formData;

      // Get name from form
      const NAME_INPUT = nameFieldInput.target.value;
      // Selects everything, other than capital and small letters
      const NAME_REGEX = /[^A-Za-z]/g;
      // Removes all selected characters
      const SANITISED_NAME_INPUT = NAME_INPUT.replace(NAME_REGEX, '');

      // Reassign name field
      FORM_DATA["name"] = SANITISED_NAME_INPUT; // Update form data with sanitized input
    },

    // Restrict input in the 'number' field to numeric characters
    restrictPhoneNumberInput(phoneNumberFieldInput) {
      // Get the form object
      const FORM_DATA = this.formData;

      // Get the number from the form
      const PHONE_NUMBER_INPUT = phoneNumberFieldInput.target.value;
      // Selects everything, other than numbers.
      const PHONE_NUMBER_REGEX = /\D/g;
      // Remove selected characters
      const SANITISED_PHONE_NUMBER_INPUT = PHONE_NUMBER_INPUT.replace(PHONE_NUMBER_REGEX, '');

      // Reassign number field
      FORM_DATA["number"] = SANITISED_PHONE_NUMBER_INPUT; // Update form data with sanitized input
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

    // Prepare cart contents for display
    cartTableContents() {
      // Gather necessary data
      const CART_IS_EMPTY = this.isCartEmpty;
      const CART_ITEMS = Object.entries(this.cart);

      // If the cart is empty, go back to Lessons page.
      if (CART_IS_EMPTY) {
        this.showCart = false;
        return [];
      }

      // Else, generate objects based on the cart,
      // which will be used to generate the table of cart items
      let cartTableEntries = [];
      CART_ITEMS.forEach(
        ([lessonID, quantity]) => {

          const LESSON = this.lessons.find((lesson) => lesson.id === parseInt(lessonID));
          cartTableEntries.push(
            {
              lesson: LESSON,
              quantity: quantity,
              subtotal: LESSON.price * quantity
            }
          )
        }
      )

      // Sort table entries by their subject,
      // else they will be ordered by their IDs,
      // which are not displayed
      cartTableEntries.sort(
        (entry1, entry2) => entry1.lesson.subject.localeCompare(entry2.lesson.subject)
      )

      return cartTableEntries;
    },

    // Calculate the total price of items in the cart
    cartPrice() {
      const CART_ITEMS = this.cartTableContents;
      if (!CART_ITEMS) {
        return 0; // Return 0 if cartTableContents is undefined or empty
      }
      const TOTAL_PRICE = CART_ITEMS.reduce(((acc, entry) => acc + entry.subtotal), 0);


      return TOTAL_PRICE;
    },

    // Check if the cart is empty
    isCartEmpty() {
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