<template>
  <q-drawer v-model="drawer"
            show-if-above
            elevated
            :mini="!drawer || miniState"
            :width="300"
            :breakpoint="500"
            bordered
            class="bg-grey-3">
    <q-btn class="nav-toggler text-white" type="button" icon="menu" unelevated @click.prevent="drawerClick"></q-btn>
    <q-scroll-area class="fit text-red-14 text-bold">
      <q-list padding>
        <div class="text-center" v-if="!miniState">
          <h6 class="q-ma-none text-dark">{{ userRole }}</h6>
          <hr class="nav-hr" />
        </div>
        <EvaluatorDrawer v-if="userRole === 'REVIEWER'" />

        <div>
          <hr class="nav-hr" />
          <q-item clickable v-ripple class="nav-item" @click="confirm_logout">
            <q-item-section avatar>
              <i class="fa-solid fa-power-off"></i>
            </q-item-section>

            <q-item-section class="text-red-15">
              Log out
            </q-item-section>
          </q-item>
        </div>
      </q-list>
    </q-scroll-area>

    <!--
      in this case, we use a button (can be anything)
      so that user can switch back
      to mini-mode
    -->
    <div class="q-mini-drawer-hide absolute" style="top: 15px; right: -17px">
      <q-btn dense
             round
             unelevated
             color="accent"
             icon="chevron_left"
             @click="miniState = true" />
    </div>
  </q-drawer>
</template>

<script>
  import { ref } from 'vue';
  import EvaluatorDrawer from "../Evaluator/DrawerItems";
  import Swal from "sweetalert2";

  const miniState = ref(false)

  export default {
    components: {
      EvaluatorDrawer
    },
    data: () => ({
      currentTime: "",
      miniState: miniState,

      // Temporary Variable (Remove later)
      userRoles: [
        "REVIEWER",
      ],
      userRole: "REVIEWER"

    }),
    watch: {
    },

    computed: {
    },

    mounted() {
      let vm = this;
      vm.getCurrentDateTime();

      setInterval(() => {
        vm.getCurrentDateTime();
      }, 1000);
    },
    methods: {
      drawerClick(e) {
        // if in "mini" state and user
        // click on drawer, we switch it to "normal" mode
        if (this.miniState) {
          this.miniState = false
          e.stopPropagation()
        } else {
          this.miniState = true
          e.stopPropagation()
        }
      },


      confirm_logout() {
        Swal.fire({
          title: 'Logging Out',
          text: 'Are you sure you want to Logout?',
          icon: 'question',
          showCancelButton: true,
          confirmButtonColor: '#3085d6',
          cancelButtonColor: '#d33',
          confirmButtonText: 'Logout',
        }).then((result) => {
          if (result.isConfirmed) {
            // this.$q.sessionStorage.clear();
            // this.$q.localStorage.clear();
            this.logout();
          }
        });
      },

      getCurrentDateTime() {
        let currentdate = new Date();
        let hours = currentdate.getHours() > 12 ? currentdate.getHours() - 12 : currentdate.getHours();
        hours = hours <= 10 ? '0' + hours : hours;
        let ampm = currentdate.getHours() > 12 ? "PM" : "AM";

        this.currentTime = `${hours}:${currentdate.getMinutes()}:${currentdate.getSeconds() < 10 ? '0'+currentdate.getSeconds(): currentdate.getSeconds()} ${ampm}`;
      },
      getToday() {
        let currentdate = new Date();

      },
      logout(){
        localStorage.clear();
        window.location.href = process.env.ADMIN_BASE_URL;
      },

    },

    setup() {
      return {
        drawer: ref(false),
      }
    }
  }
</script>
