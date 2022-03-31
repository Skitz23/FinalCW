<template>
    <div>
     <div class="cart w-100">
        <div class="container">
            <div>
            <div class="row ">
                <div class="col-12">
                <h2 class="font-weight-bolder mt-0">
                    <button class="btn btn-link bg-dark btn-dark mr-2" @click="$emit('togglecart')">
                    <i class="fa fa-chevron-left"></i>
                    </button>
                    Basket
                </h2>
                <p>{{cart_items_count}} items</p>
                </div>
                <div class="col-12" v-for="(item, index) in shopping_cart_items" :key="index">
                <div class="d-flex justify-content-betweem py-2">
                    <div class="media py-2">
                    <img class="mr-3" width="72px" :src="BACKEND_URL + '/public/' + item.image.split('/').pop()" alt="Generic placeholder image">
                    <div class="media-body">
                        <h6>{{item.subject}}</h6>
                        <p class="mt-0">{{item.location}}</p>
                        <span class="font-weight-bolder float-left">
                        <span>${{item.price}}</span>
                        </span>
                    </div>
                    </div>

                    <button style="max-height: 40px !important; width:100px;"
                    class="btn text-danger font-weight-bolder bg-warning btn-sm ml-auto pull-right"
                    @click="$emit('removefromcart', item)">
                    <i class="fa fa-trash"></i> Remove
                    </button>

                </div>
                </div>
            </div>
            </div>

            <div class="card-action">
            <a class="btn text-primary bg-dark" href="#checkout" @click="checkout = true">
                Proceed To Checkout
            </a>
            </div>



            <div class="card-text row w-50" v-if="checkout">
            <div class="col-12 col-sm-8" id="checkout">
                <h2 class="font-weight-bolder mt-4">Checkout</h2>
                <div class="form-group">
                <label>Name</label>
                <input type="text" v-model="data.name" @input="validateName" class="form-control"
                    placeholder="Enter Full Name">
                </div>
                <div class="form-group">
                <label>Phone</label>
                <input type="number" v-model="data.phone" @input="validatePhone" class="form-control"
                    placeholder="Enter Phone number">
                </div>
                <button type="button" class="btn text-primary bg-dark" v-if="is_valid_name && is_valid_phone"
                v-on:click="$emit('doplaceorder', data)">
                Checkout
                </button>
            </div>
            </div>
        </div>
        </div>
        </div>
</template>


<script>
    export default { 
        name: "CheckoutComponent",
        props: {
            shopping_cart_items: {
                type: Array,
                default: () => ([])
            },
            cart_items_count: {
                type: Number,
                default: 0
            },
            BACKEND_URL: {
                type: String,
                default: ""
            }
        },
        data() {
            return {
                data: {
                    name: "",
                    phone: ""
                },
                checkout: false,
                is_valid_name: false,
        is_valid_phone: false,
            }
        },

        methods: {
            validateName() {
                this.is_valid_name = /^[a-zA-Z]+$/.test(this.data.name) ? true : false;
                console.log(this.is_valid_name);
            },

            validatePhone() {
                this.is_valid_phone = /^[0-9]+$/.test(this.data.phone) ? true : false;
                console.log(this.is_valid_phone);
            },
        }
    }

</script>