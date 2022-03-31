<template>

    <div class="row">

      <div class="col-12" v-for="(lesson, index) in filteredLessons" :key="index">
            <div class="card my-2 border-0 shadow d-flex flex-row" style="height: 200px;">
              <img class="card-img-top w-25" :src="BACKEND_URL + '/public/' + lesson.image.split('/').pop()"
                alt="Card image cap">
              <div class="card-body d-flex flex-column justify-content-between">
                <div class="card-top">
                  <i :class="`text-primary bg-dark icon fa ${lesson.icon}`"></i>
                  <h4>{{lesson.subject}}</h4>
                  <h6 class="card-title">Location: {{lesson.location}}</h6>
                </div>

                <div class="card-text mt-2">
                  <span class="font-weight-bold float-left">
                    <span>${{lesson.price}}</span>
                    <br />
                    <span class="badge badge-dark text-primary" v-if="lesson.space > 0">
                      {{lesson.space}} Spaces
                    </span>
                  </span>
                  <button :disabled="lesson.space == 0"
                    class="btn text-primary btn-dark mt-2 btn-sm float-md-right text-uppercase"
                    @click="$emit('addtocart', lesson)">
                    Add To Basket
                  </button>
                </div>
              </div>
            </div>
          </div>
    </div>

</template>


<script>
    export default {
      name: "LessonComponent",
        props: {
            filteredLessons: {
                type: Array,
                default: () => ([])
            },
            BACKEND_URL: {
                type: String,
                default: ""
            }
        }
    }

</script>