<template>
  <div>
  
    <section class="lessons">
      <div class="container py-4">
        <div class="row">

          <div class="col-12 sticky-top bg-white">
            <div class="alert alert-warning alert-dismissible fade show" role="alert" v-if="message">
              <strong>Success</strong> Order Successful
              <button type="button" class="close" data-dismiss="alert" aria-label="Close" @click="message = false">
                <span aria-hidden="true">&times;</span>
              </button>
            </div>


            <h1 class="display-4">
              <div class="font-weight-bolder text-center my-5">Subjects Of Education</div>
              <button class="float-right btn btn-link btn-md bg-dark position-relative" v-if='cart_items_count > 0'
                @click="cart = !cart">
                <i class="fas fa-shopping-cart"></i>
                Cart
                <span class="badge badge-primary"
                  style="position: absolute; top: -5px; right: -20px;">{{cart_items_count}}
                  items</span>
              </button>
            </h1>

            <div class="row">
              <div class="col-12 col-sm-6">
                <div class="input-group mb-3 mr-sm-4">
                  <input type="search" v-model="search" @input="getDataFromServer" class="form-control"
                    placeholder="Search Subjects....." aria-label="Search Subjects.....">
                  <div class="input-group-append ">
                    <span class="fa fa-search input-group-text bg-dark text-primary pt-2"></span>
                  </div>
                </div>
              </div>

              <div class="col-8 col-sm-6">
                <div class="form-group row">
                  <div class="col-8">
                    <select type="search" v-model="sort_by" @change="doSort('desc')" class="form-control">
                      <option v-for="(key, index) in sort_params" :key="index+'sp'" :value="key">
                        Sort: {{key}}
                      </option>
                    </select>
                  </div>
                  <div class="col-4 px-1">
                    <button class="btn btn-link text-primary bg-dark" @click="doSort('asc')">
                      <i class="fa fa-chevron-circle-up"></i>
                    </button>
                    <button class="btn btn-link text-primary bg-dark" @click="doSort('desc')">
                      <i class="fa fa-chevron-circle-down"></i>
                    </button>
                  </div>
                </div>
              </div>

            </div>
            <hr class="border-dark" />
          </div>

            <span v-if="cart && cart_items_count > 0">
              <CheckoutComponent 
                :shopping_cart_items="shopping_cart_items" 
                :BACKEND_URL="BACKEND_URL" 
                :cart_items_count="cart_items_count" 
                @togglecart="cart = !cart" 
                @removefromcart="removeFromCart"
                @doplaceorder="doPlaceOrder"
                ></CheckoutComponent>
            </span>


          <!-- lesson -->
          <span  v-else>
            <LessonComponent
              :filteredLessons="filteredLessons"
              :BACKEND_URL="BACKEND_URL" 
              @addtocart="addToCart"></LessonComponent>
          </span>


        </div>
      </div>
    </section>
  </div>
</template>

<script>
// const emailRegex = RegExp(/^[a-zA-Z0-9.!#$%&’*+/=?^_`{|}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$/);
const BACKEND_URL = 'https://nodejs-vue-backend.herokuapp.com';

// const headers = { 
//     'Accept': 'application/json',
//     'Content-Type': 'application/json;charset=UTF-8'
// };

import LessonComponent from "./components/LessonComponent.vue"
import CheckoutComponent from "./components/CheckoutComponent.vue"
export default {
  name: 'App',
  data() {
    return {
      BACKEND_URL,
        search: '',
        lessons: [],
        space: 5,
        cart: false,
        shopping_cart_items: [],
        name: "",
        phone: "",
        
        message: false,
        sort_by: 'location',
        sort_params: ['Subject', 'Location', 'Price', 'Space']
    }
  },
  components: {
    LessonComponent,
    CheckoutComponent
  },

  computed: {
                filteredLessons: function () {
                  return this.lessons;
                },
    
                cart_items_count() {
                  return this.shopping_cart_items.length;
                }
              },
    methods:{
        addToCart(lesson) {
            if (lesson.space > 0) {
              let lessonIndex = this.lessons.findIndex((obj) => obj.image === lesson.image);
              if (this.lessons[lessonIndex]?.space > 0) {
                this.shopping_cart_items.push(lesson);
                this.lessons[lessonIndex].space--;
              } else this.shopping_cart_items.push(lesson);
            }
        },

        removeFromCart(lesson) {
            let lessonIndex = this.lessons.findIndex((obj) => obj.image === lesson.image);
            let cartIndex = this.shopping_cart_items.findIndex((obj) => obj.image === lesson.image);
            if (this.lessons[lessonIndex]?.space < 5) {
                this.shopping_cart_items.pop(this.shopping_cart_items[cartIndex]);
                this.lessons[lessonIndex].space++;
            } else this.shopping_cart_items.pop(this.shopping_cart_items[cartIndex]);
            if (this.shopping_cart_items?.length == 0) {
                this.cart = false;
            }
        },

        getDataFromServer() {
            fetch(BACKEND_URL + '/search' + `?q=${this.search}`)
                .then(res => res.json())
                .then(lessons => this.lessons = lessons);
        },

        doSort(order) {
            if (order == 'desc') {
                this.lessons.sort((a, b) => a[this.sort_by] < b[this.sort_by] ? 1 : -1);
            } else this.lessons.sort((a, b) => a[this.sort_by] > b[this.sort_by] ? 1 : -1);
        },     

        doPlaceOrder(orderdata) {
            // e.preventDefault();
            let items = {};
            // Cleaning the data
            this.shopping_cart_items.forEach(item => {
                if (items[item._id]) {
                items[item._id].space++;
                } else {
                items[item._id] = { space: 1 };
                }
            });

            items = Object.keys(items).map(key => {
                return {
                _id: key,
                space: items[key].space
                };
            });


            const data = {
                name: orderdata.name,
                phone: orderdata.phone,
                items
            };

            fetch(BACKEND_URL + '/checkout', {
                method: 'POST',
                headers: {
                'Content-Type': 'application/json'
                },
                body: JSON.stringify(data)
            }).then(res => {
                if (res.ok)
                return res.json();
            }).then(orderRegistered => {
                const { insertedId } = orderRegistered.order;
                fetch(BACKEND_URL + '/lessons/' + insertedId, {
                method: 'PUT',
                headers: {
                    'Content-Type': 'application/json'
                }
                })
                .then(res => {
                    if (res.ok) {
                    this.shopping_cart_items = [];
                    this.cart = false;
                    this.message = true;
                    }
                });
            });
        }
    },
    mounted(){
        this.getDataFromServer()
    }
}
</script>

<style>
.icon{
    position: absolute;
    top: 16px;
    right: 16px;
    width: 32px;
    height: 32px;
    border-radius: 50%;
    text-align: center;
    padding-top: 8px;
}

.card{
    border-radius: 24px;
}


.bg-primary-light{
    background-color: #c8e2ff !important;
}

.bg-danger-light{
    background-color: #ffd5d9 !important;
}
</style>
