<template>
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
                            <button class="btn btn-outline-secondary m-1"
                                @click="removeFromCart(cartTableEntry.lesson.id)">
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
</template>

<script>
export default {
    props: {
        cart: Object,
        lessons: Array,
        isCartEmpty: Boolean,
    },
    data() {
        return {
            formData: { // Form data for customer details
                name: "",
                number: ""
            }
        }
    },
    computed: {
        // Prepare cart contents for display
        cartTableContents() {
            // Gather necessary data
            const CART_IS_EMPTY = this.isCartEmpty;
            const CART_ITEMS = Object.entries(this.cart);

            // If the cart is empty, go back to Lessons page.
            if (CART_IS_EMPTY) {
                // this.showCart = false;
                this.$emit("show-cart-false")
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
        }
    },
    methods: {
        addToCart(lessonId) {
            this.$emit('add-item-to-cart', lessonId);
        },
        removeFromCart(lessonId) {
            this.$emit('remove-item-from-cart', lessonId);
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
        }
    }
}
</script>