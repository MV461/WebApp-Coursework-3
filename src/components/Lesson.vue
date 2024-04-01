<template>
    <div class="row row-cols-1 row-cols-md-2 row-cols-lg-3 g-4">
        <div class="col" v-for="lesson in lessons" :key="lesson.id">
            <div class="card text-center">
                <div class="row g-0">
                    <div class="col-md-4 d-flex justify-content-center align-items-center">
                        <span class="card-icon p-2">
                            <i :class="lesson.icon"></i>
                        </span>
                    </div>
                    <div class="col-md-8 bg-light">
                        <div class="card-header">
                            {{ lesson.subject }} Lessons
                        </div>
                        <div class="card-body">
                            <table class="table table-bordered">
                                <tbody>
                                    <tr>
                                        <td><strong>Location</strong></td>
                                        <td>{{ lesson.location }}</td>
                                    </tr>
                                    <tr>
                                        <td><strong>Price</strong></td>
                                        <td>{{ lesson.price }} AED</td>
                                    </tr>
                                    <tr>
                                        <td><strong>Available Spaces</strong></td>
                                        <td>{{ lesson.spaces }}</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                        <div class="card-footer">
                            <button @click="addToCart(lesson.id)" :disabled="lesson.spaces === 0"
                                class="btn btn-primary m-2">
                                {{ lesson.spaces === 0 ? 'No Spaces Available' : 'Add to Cart' }}
                            </button>
                            <!-- "Remove from Cart" Button END -->
                            <button v-if="cart[lesson.id]" @click="removeFromCart(lesson.id)"
                                class="btn btn-danger m-2">
                                Remove from Cart
                            </button>
                            <!-- "Remove from Cart" Button END -->
                        </div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: {
        lessons: Array,
        cart: Object
    },
    methods: {
        addToCart(lessonId) {
            this.$emit('add-item-to-cart', lessonId);
        },
        removeFromCart(lessonId) {
            this.$emit('remove-item-from-cart', lessonId);
        }
    }
}
</script>