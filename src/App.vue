<template>
  <v-app>
    <router-view></router-view>
  </v-app>
</template>

<script>
import axios from "axios";
import { mapGetters } from "vuex";
export default {
  name: "App",

  data() {
    return {
      selectedComponent: "Step1",
      tabs: [
        {
          id: "1",
          title: "Otel ve Tarih Seçimi",
          icon: "ti-user",
          component: "Step1",
          beforeChange: this.validateStep1,
        },
        {
          id: "2",
          title: "Oda Tipi ve Manzara Seçimi",
          icon: "ti-settings",
          component: "Step2",
          beforeChange: this.validateStep2,
        },
        {
          id: "3",
          title: "Önizleme ve Ödeme İşlemleri",
          icon: "ti-location-pin",
          component: "Step3",
        },
      ],
    };
  },
  computed: {
    ...mapGetters([
      "getAdultNumber",
      "getHotelName",
      "getPickedRoomType",
      "getPickedRoomScenic",
      "getSelectedHotelDetails",
      "getCheckIn",
      "getCheckOut",
      "getChildNumber",
      "getPrice",
      "getCustomerName",
      "getCardNumber",
      "getCardMonth",
      "getCardYear",
      "getCardCVV",
    ]),
  },
  methods: {
    validateStep1: function() {
      return new Promise((resolve, reject) => {
        if (this.getHotelName && this.getAdultNumber) {
          resolve(true);
        } else {
          this.dialog = true;
          reject();
        }
      });
    },
    validateStep2: function() {
      return new Promise((resolve, reject) => {
        if (this.getPickedRoomType && this.getPickedRoomScenic) {
          resolve(true);
        } else {
          this.dialog = true;
          reject();
        }
      });
    },
    onComplete() {
      axios
        .post(
          "https://5f6d939160cf97001641b049.mockapi.io/tkn/hotel-bookings",
          {
            hotel_id: this.getSelectedHotelDetails[0].hotel_id,
            start_date: this.getCheckIn,
            end_date: this.getCheckOut,
            adult: this.getAdultNumber,
            child: this.getChildNumber === "" ? null : this.getChildNumber,
            room_type: this.getPickedRoomType,
            room_scnic: this.getPickedRoomScenic,
            price: this.getPrice,
            coupon_code: "CODE100",
            card_name: this.getCustomerName,
            card_number: this.getCardNumber,
            card_date_month: this.getCardMonth,
            card_date_year: this.getCardYear,
            card_cvv: this.getCardCVV,
          }
        )
        .then((response) => {
          console.log(response);
          this.$router.push("/details");
        });
    },
  },
};
</script>
