<template>
  <v-layout align-center justify-center class="white">
    <v-container>
      <v-row>
        <v-col cols="12">
          <div class="hotel-details">
            <h1 hotel-details__header>
              {{ getHotelName }}
              <span>{{ selectedCity }}</span>
            </h1>
            <p>
              <strong>Check-in :</strong> <span>{{ getCheckIn }}</span>
              <strong>Check-out :</strong> <span>{{ getCheckOut }}</span>
              <strong>Adults :</strong> <span>{{ getAdultNumber }}</span>
              <strong>Children :</strong>
              <span>{{ getChildNumber ? getChildNumber : 0 }}</span>
            </p>
          </div>
        </v-col>
      </v-row>
      <!-- Oda Tipi Seçimi -->
      <v-row>
        <v-col
          cols="12"
          sm="6"
          md="4"
          class="mt-4"
          v-for="roomtype in roomTypes"
          :key="roomtype.id"
        >
          <label class="radio-img">
            <input
              type="radio"
              name="layout"
              :value="roomtype.title"
              v-model="pickedRoomType"
              required
            />
            <v-card class="mx-auto image" max-width="344">
              <v-card-title>
                {{ roomtype.title }}
              </v-card-title>
              <v-img
                lazy-src="https://picsum.photos/id/11/10/6"
                max-height="150"
                max-width="250"
                :src="roomtype.photo"
              ></v-img>
              <div>
                <v-card-text class="card-info">
                  {{ dateDifference }} gün
                </v-card-text>
                <v-card-text class="card-info">
                  {{ roomPrice(roomtype.id) }}TL
                </v-card-text>
                <v-spacer></v-spacer>
                <v-card-subtitle> {{ getAdultNumber }} Adults </v-card-subtitle>
              </div>
            </v-card>
          </label>
        </v-col>
      </v-row>
      <!-- Manzara Tipi Seçimi -->
      <v-row>
        <v-col
          cols="12"
          sm="6"
          md="4"
          class="mt-4"
          v-for="roomScenic in roomScenics"
          :key="roomScenic.id"
        >
          <label class="radio-img">
            <input
              type="radio"
              name="layout2"
              :value="roomScenic.title"
              v-model="pickedRoomScenic"
              required
            />
            <v-card class="mx-auto image" max-width="344">
              <v-card-title>
                {{ roomScenic.title }}
              </v-card-title>
              <v-img
                :lazy-src="roomScenic.photo"
                max-height="150"
                max-width="250"
                :src="roomScenic.photo"
              ></v-img>
              <div>
                <v-card-text class="card-info">
                  Fiyat Etki Oranı
                </v-card-text>
                <v-card-text class="card-info">
                  +{{ roomScenic.price_rate }}%
                </v-card-text>
              </div>
            </v-card>
          </label>
        </v-col>
      </v-row>
    </v-container>
  </v-layout>
</template>
<script>
import { mapGetters } from "vuex";
export default {
  data() {
    return {
      radioGroup: 1,
    };
  },
  computed: {
    ...mapGetters([
      "getHotelName",
      "getCheckIn",
      "getCheckOut",
      "getAdultNumber",
      "getChildNumber",
      "getSelectedHotelDetails",
    ]),
    roomTypes() {
      return this.getSelectedHotelDetails[0].room_type;
    },

    roomScenics() {
      return this.getSelectedHotelDetails[0].room_scenic;
    },
    dateDifference() {
      let date1 = new Date(this.getCheckIn);
      let date2 = new Date(this.getCheckOut);
      let diffTime = Math.abs(date2 - date1);
      let diffDays = Math.ceil(diffTime / (1000 * 60 * 60 * 24));
      return diffDays;
    },
    pickedRoomType: {
      get() {
        return this.$store.getters.getPickedRoomType;
      },
      set(value) {
        this.$store.commit("setPickedRoomType", value);
      },
    },
    pickedRoomScenic: {
      get() {
        return this.$store.getters.getPickedRoomScenic;
      },
      set(value) {
        this.$store.commit("setPickedRoomScenic", value);
      },
    },
    selectedCity() {
      console.log(this.getSelectedHotelDetails[0].city);
      return this.getSelectedHotelDetails
        ? this.getSelectedHotelDetails[0].city
        : "-";
    },
  },
  methods: {
    roomPrice(id) {
      console.log("id", id);
      let roomInfo = this.roomTypes.filter((item) =>
        item.id === id ? item.price : null
      );
      console.log("roomInfo", roomInfo);
      let hotelprice =
        roomInfo[0].price * this.dateDifference * this.getAdultNumber;
      this.$store.commit("setPrice", hotelprice);
      return hotelprice;
    },
  },
};
</script>
<style scoped>
.card-info {
  display: inline-block !important;
  width: 50%;
  font-size: 1rem;
  font-weight: 500;
}
</style>
