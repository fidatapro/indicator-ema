<template>
  <div >
    <v-data-table
      :headers="headers_"
      :items="data_table"
      sort-by="calories"
      class="custom_table_class"
      :options.sync="pagination"
      @update:page="updatePage()"
      :footer-props="footerProps"
      :loading="loading"
      classPaging="pt-2"
      hideDefaultHeader
    >
      <template v-slot:header="{ props: { headers } }">
        <thead>
          <tr :title="headers.length" class="border-table">
            <th rowspan="2" class="border-table" width="20px"></th>
            <th
              rowspan="2"
              class="border-table text-center"
              style="min-width: 70px"
              width="150px"
            >
              1-500
            </th>
            <th
              rowspan="2"
              class="border-table text-center"
              style="min-width: 150px"
              width="200px"
            >
              NAME
            </th>
            <th rowspan="2" class="border-table text-center" width="110px">
              PRICE
            </th>
            <th
              :colspan="colspan1h()"
              class="text-center border-table"
              style="background-color: #3b3b3b"
              v-if="check1H()"
            >
              <span style="color: white; font-size: 16px">1H</span>
            </th>
            <th
              :colspan="colspan4h()"
              class="text-center border-table"
              style="background-color: #3b3b3b"
              v-if="check4H()"
            >
              <span style="color: white; font-size: 16px">4H</span>
            </th>
            <th
              :colspan="colspan24h()"
              class="text-center border-table"
              style="background-color: #3b3b3b"
              v-if="check24H()"
            >
              <span style="color: white; font-size: 16px">24H</span>
            </th>
          </tr>
          <tr>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_1h_12')"
            >
              EMA12
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_1h_21')"
            >
              EMA21
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_1h_26')"
            >
              EMA26
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_1h_50')"
            >
              EMA50
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_1h_100')"
            >
              EMA100
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_1h_200')"
            >
              EMA200
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_4h_12')"
            >
              EMA12
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_4h_21')"
            >
              EMA21
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_4h_26')"
            >
              EMA26
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_4h_50')"
            >
              EMA50
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_4h_100')"
            >
              EMA100
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_4h_200')"
            >
              EMA200
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_24h_12')"
            >
              EMA12
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_24h_21')"
            >
              EMA21
            </th>
            <!-- <th
              class="border-table"
              v-if="arrHeader.includes('ema_24h_26')"
            >
              EMA26
            </th> -->
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_24h_50')"
            >
              EMA50
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_24h_100')"
            >
              EMA100
            </th>
            <th
              class="border-table"
              v-if="arrHeader.includes('ema_24h_200')"
            >
              EMA200
            </th>
          </tr>
        </thead>
      </template>
      <template v-slot:[`item.icon`]="{ item }">
        <v-btn depressed @click="addFavorite(item)">
          <div class="d-flex">
            <svg
              xmlns="http://www.w3.org/2000/svg"
              viewBox="0 0 24 24"
              role="img"
              aria-hidden="true"
              class="v-icon__svg"
              :class="{ 'color-favorite': checkFavorite(item) }"
            >
              <path
                d="M12,17.27L18.18,21L16.54,13.97L22,9.24L14.81,8.62L12,2L9.19,8.62L2,9.24L7.45,13.97L5.82,21L12,17.27Z"
              ></path>
            </svg>
          </div>
        </v-btn>
      </template>
      <template v-slot:top>
        <v-toolbar flat class="py-2">
          <v-toolbar-title>Ema table</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <div style="width: 400px" class="d-flex align-center flex-grow-0">
            <v-text-field
              label="Search ..."
              append-icon="mdi-magnify"
              hide-details
              style="width: 50px !important"
              v-model="form.search"
            ></v-text-field>
          </div>
          <v-spacer></v-spacer>
          <v-btn depressed @click="clickFilterColumn()">
            <div class="d-flex">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                role="img"
                aria-hidden="true"
                class="v-icon__svg"
              >
                <path
                  d="M12,15.5A3.5,3.5 0 0,1 8.5,12A3.5,3.5 0 0,1 12,8.5A3.5,3.5 0 0,1 15.5,12A3.5,3.5 0 0,1 12,15.5M19.43,12.97C19.47,12.65 19.5,12.33 19.5,12C19.5,11.67 19.47,11.34 19.43,11L21.54,9.37C21.73,9.22 21.78,8.95 21.66,8.73L19.66,5.27C19.54,5.05 19.27,4.96 19.05,5.05L16.56,6.05C16.04,5.66 15.5,5.32 14.87,5.07L14.5,2.42C14.46,2.18 14.25,2 14,2H10C9.75,2 9.54,2.18 9.5,2.42L9.13,5.07C8.5,5.32 7.96,5.66 7.44,6.05L4.95,5.05C4.73,4.96 4.46,5.05 4.34,5.27L2.34,8.73C2.21,8.95 2.27,9.22 2.46,9.37L4.57,11C4.53,11.34 4.5,11.67 4.5,12C4.5,12.33 4.53,12.65 4.57,12.97L2.46,14.63C2.27,14.78 2.21,15.05 2.34,15.27L4.34,18.73C4.46,18.95 4.73,19.03 4.95,18.95L7.44,17.94C7.96,18.34 8.5,18.68 9.13,18.93L9.5,21.58C9.54,21.82 9.75,22 10,22H14C14.25,22 14.46,21.82 14.5,21.58L14.87,18.93C15.5,18.67 16.04,18.34 16.56,17.94L19.05,18.95C19.27,19.03 19.54,18.95 19.66,18.73L21.66,15.27C21.78,15.05 21.73,14.78 21.54,14.63L19.43,12.97Z"
                ></path>
              </svg>
              <div class="mt-auto">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 24 24"
                  role="img"
                  aria-hidden="true"
                  class="v-icon__svg"
                  style="font-size: 14px; height: 14px; width: 14px"
                >
                  <path
                    d="M12,17C10.89,17 10,16.1 10,15C10,13.89 10.89,13 12,13A2,2 0 0,1 14,15A2,2 0 0,1 12,17M18,20V10H6V20H18M18,8A2,2 0 0,1 20,10V20A2,2 0 0,1 18,22H6C4.89,22 4,21.1 4,20V10C4,8.89 4.89,8 6,8H7V6A5,5 0 0,1 12,1A5,5 0 0,1 17,6V8H18M12,3A3,3 0 0,0 9,6V8H15V6A3,3 0 0,0 12,3Z"
                  ></path>
                </svg>
              </div>
            </div>
          </v-btn>
          <v-btn
            depressed
            @click="clickFavorite()"
            :color="check_favorite ? 'primary' : 'default'"
          >
            <div class="d-flex">
              <svg
                xmlns="http://www.w3.org/2000/svg"
                viewBox="0 0 24 24"
                role="img"
                aria-hidden="true"
                class="v-icon__svg"
              >
                <path
                  d="M12,17.27L18.18,21L16.54,13.97L22,9.24L14.81,8.62L12,2L9.19,8.62L2,9.24L7.45,13.97L5.82,21L12,17.27Z"
                ></path>
              </svg>
              <div class="mt-auto">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 24 24"
                  role="img"
                  aria-hidden="true"
                  class="v-icon__svg"
                  style="font-size: 14px; height: 14px; width: 14px"
                >
                  <path
                    d="M12,17C10.89,17 10,16.1 10,15C10,13.89 10.89,13 12,13A2,2 0 0,1 14,15A2,2 0 0,1 12,17M18,20V10H6V20H18M18,8A2,2 0 0,1 20,10V20A2,2 0 0,1 18,22H6C4.89,22 4,21.1 4,20V10C4,8.89 4.89,8 6,8H7V6A5,5 0 0,1 12,1A5,5 0 0,1 17,6V8H18M12,3A3,3 0 0,0 9,6V8H15V6A3,3 0 0,0 12,3Z"
                  ></path>
                </svg>
              </div>
            </div>
          </v-btn>
        </v-toolbar>
      </template>
      <!-- eslint-disable-next-line -->
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
      <!-- eslint-disable-next-line -->
      <template v-slot:item.coin_name="{ item }">
        <img
          style="width: 30px; padding-right: 8px; vertical-align: middle"
          :src="getSrc(item.coin_symbol)"
        />
        {{ item.coin_name }}
      </template>
      <template v-slot:[`item.coin_price`]="{ item }">
        <div
          :class="getStatus(item.status, item.id)"
          class="py-2 d-flex flex-wrap flex-grow-1"
        >
          <span>
            {{ `${getRealValue(item.coin_price)}` }}
          </span>
        </div>
      </template>

      <!-- eslint-disable-next-line -->
      <template v-slot:[`item.ema_1h_12`]="{ item }">
        <span :class="getEmaColor(item.ema_1h_12, item.coin_price)">
          {{ getRealValue(item.ema_1h_12) }}
        </span>
      </template>
      <template v-slot:[`item.ema_1h_21`]="{ item }">
        <span :class="getEmaColor(item.ema_1h_21, item.coin_price)">
          {{ getRealValue(item.ema_1h_21) }}
        </span>
      </template>
      <template v-slot:[`item.ema_1h_26`]="{ item }">
        <span :class="getEmaColor(item.ema_1h_26, item.coin_price)">
          {{ getRealValue(item.ema_1h_26) }}
        </span>
      </template>
      <template v-slot:[`item.ema_1h_50`]="{ item }">
        <span :class="getEmaColor(item.ema_1h_50, item.coin_price)">
          {{ getRealValue(item.ema_1h_50) }}
        </span>
      </template>
      <template v-slot:[`item.ema_1h_100`]="{ item }">
        <span :class="getEmaColor(item.ema_1h_100, item.coin_price)">
          {{ getRealValue(item.ema_1h_100) }}
        </span>
      </template>
      <template v-slot:[`item.ema_1h_200`]="{ item }">
        <span :class="getEmaColor(item.ema_1h_200, item.coin_price)">
          {{ getRealValue(item.ema_1h_200) }}
        </span>
      </template>
      <template v-slot:[`item.ema_4h_12`]="{ item }">
        <span :class="getEmaColor(item.ema_4h_12, item.coin_price)">
          {{ getRealValue(item.ema_4h_12) }}
        </span>
      </template>
      <template v-slot:[`item.ema_4h_21`]="{ item }">
        <span :class="getEmaColor(item.ema_4h_21, item.coin_price)">
          {{ getRealValue(item.ema_4h_21) }}
        </span>
      </template>
      <template v-slot:[`item.ema_4h_26`]="{ item }">
        <span :class="getEmaColor(item.ema_4h_26, item.coin_price)">
          {{ getRealValue(item.ema_4h_26) }}
        </span>
      </template>
      <template v-slot:[`item.ema_4h_50`]="{ item }">
        <span :class="getEmaColor(item.ema_4h_50, item.coin_price)">
          {{ getRealValue(item.ema_4h_50) }}
        </span>
      </template>
      <template v-slot:[`item.ema_4h_100`]="{ item }">
        <span :class="getEmaColor(item.ema_4h_100, item.coin_price)">
          {{ getRealValue(item.ema_4h_100) }}
        </span>
      </template>
      <template v-slot:[`item.ema_4h_200`]="{ item }">
        <span :class="getEmaColor(item.ema_4h_200, item.coin_price)">
          {{ getRealValue(item.ema_4h_200) }}
        </span>
      </template>
      <template v-slot:[`item.ema_24h_12`]="{ item }">
        <span :class="getEmaColor(item.ema_24h_12, item.coin_price)">
          {{ getRealValue(item.ema_24h_12) }}
        </span>
      </template>
      <template v-slot:[`item.ema_24h_21`]="{ item }">
        <span :class="getEmaColor(item.ema_24h_21, item.coin_price)">
          {{ getRealValue(item.ema_24h_21) }}
        </span>
      </template>
      <!-- <template v-slot:[`item.ema_24h_26`]="{ item }">
        <span :class="getEmaColor(item.ema_24h_26, item.coin_price)">
          {{ getRealValue(item.ema_24h_26) }}
        </span>
      </template> -->
      <template v-slot:[`item.ema_24h_50`]="{ item }">
        <span :class="getEmaColor(item.ema_24h_50, item.coin_price)">
          {{ getRealValue(item.ema_24h_50) }}
        </span>
      </template>
      <template v-slot:[`item.ema_24h_100`]="{ item }">
        <span :class="getEmaColor(item.ema_24h_100, item.coin_price)">
          {{ getRealValue(item.ema_24h_100) }}
        </span>
      </template>
      <template v-slot:[`item.ema_24h_200`]="{ item }">
        <span :class="getEmaColor(item.ema_24h_200, item.coin_price)">
          {{ getRealValue(item.ema_24h_200) }}
        </span>
      </template>
    </v-data-table>
    <dialogColumn
      ref="dialogColumn"
      typeColumn="ema"
      :listFields="listFields"
      @reset="initialize()"
    ></dialogColumn>
  </div>
</template>
<script>
import {
  getData,
  getStatus,
  getSrc,
  getRsiColor,
  getRealValue,
  debounce,
  flooNumber,
  getEmaColor,
} from "@/views/function";
import JsonSearch from "search-array";
export default {
  components: {
    dialogColumn: () => import("@/views/components/dialog-column.vue"),
  },
  data: () => ({
    isDialogShow: false,
    getStatus,
    getSrc,
    getRealValue,
    getRsiColor,
    flooNumber,
    getEmaColor,
    headers: [
      {
        align: "start",
        sortable: false,
        value: "icon",
      },
      {
        align: "center",
        sortable: false,
        value: "rank",
        width: "70px",
      },
      { value: "coin_name", align: "start", width: "150" },
      { value: "coin_price", align: "start", width: "auto" },

      { text: "EMA 1 hour 12", value: "ema_1h_12", align: "center" },
      { text: "EMA 1 hour 21", value: "ema_1h_21", align: "center" },
      { text: "EMA 1 hour 26", value: "ema_1h_26", align: "center" },
      { text: "EMA 1 hour 50", value: "ema_1h_50", align: "center" },
      { text: "EMA 1 hour 100", value: "ema_1h_100", align: "center" },
      { text: "EMA 1 hour 200", value: "ema_1h_200", align: "center" },
      { text: "EMA 4 hour 12", value: "ema_4h_12", align: "center" },
      { text: "EMA 4 hour 21", value: "ema_4h_21", align: "center" },
      { text: "EMA 4 hour 26", value: "ema_4h_26", align: "center" },
      { text: "EMA 4 hour 50", value: "ema_4h_50", align: "center" },
      { text: "EMA 4 hour 100", value: "ema_4h_100", align: "center" },
      { text: "EMA 4 hour 200", value: "ema_4h_200", align: "center" },
      { text: "EMA 24hour 12", value: "ema_24h_12", align: "center" },
      { text: "EMA 24hour 21", value: "ema_24h_21", align: "center" },
      // { text: "EMA 24hour 26", value: "ema_24h_26", align: "center" },
      { text: "EMA 24hour 50", value: "ema_24h_50", align: "center" },
      { text: "EMA 24hour 100", value: "ema_24h_100", align: "center" },
      { text: "EMA 24hour 200", value: "ema_24h_200", align: "center" },
    ],
    pagination: {
      page: 1,
      perpage: 25,
    },
    data_table: [],
    data: [],
    loading: true,
    footerProps: {
      showFirstLastPage: true,
      showCurrentPage: true,
      itemsPerPageOptions: [25, 35, 50, -1],
    },
    form: {
      search: null,
    },
    check_favorite: false,
    headers_: [],
    field1H: [
      "ema_1h_12",
      "ema_1h_21",
      "ema_1h_26",
      "ema_1h_50",
      "ema_1h_100",
      "ema_1h_200",
    ],
    field4H: [
      "ema_4h_12",
      "ema_4h_21",
      "ema_4h_26",
      "ema_4h_50",
      "ema_4h_100",
      "ema_4h_200",
    ],
    field24H: [
      "ema_24h_12",
      "ema_24h_21",
      // "ema_24h_26",
      "ema_24h_50",
      "ema_24h_100",
      "ema_24h_200",
    ],
  }),
  watch: {
    "form.search": {
      handler: debounce(function (value) {
        this.handleSearch(value);
      }, 500),
    },
  },
  created() {
    this.initialize();
  },
  methods: {
    async initialize() {
      this.loading = true;
      const coin_data = await getData();
      this.data = coin_data.map((x) => ({
        rank: x.rank,
        coin_symbol: x.coin_symbol,
        coin_name: x.coin_name,
        coin_price: x.coin_price,
        //fiels
        ema_1h_12: x.ema_1h_12,
        ema_1h_21: x.ema_1h_21,
        ema_1h_26: x.ema_1h_26,
        ema_1h_50: x.ema_1h_50,
        ema_1h_100: x.ema_1h_100,
        ema_1h_200: x.ema_1h_200,

        ema_4h_12: x.ema_4h_12,
        ema_4h_21: x.ema_4h_21,
        ema_4h_26: x.ema_4h_26,
        ema_4h_50: x.ema_4h_50,
        ema_4h_100: x.ema_4h_100,
        ema_4h_200: x.ema_4h_200,
        ema_24h_12: x.ema_24h_12,
        ema_24h_21: x.ema_24h_21,
        // ema_24h_26: x.ema_24h_26 || 0,
        ema_24h_50: x.ema_24h_50,
        ema_24h_100: x.ema_24h_100,
        ema_24h_200: x.ema_24h_200,
      }));
      this.data_table = this.data;
      this.$refs.dialogColumn.setLisColumnShow();
      this.getColumnShow();
      this.loading = false;
      setInterval(() => this.initialize(), 5 * 60 * 1000);
    },
    getColumnShow() {
      const ema_colum = localStorage.getItem("EMA_COLUMN");
      if (!ema_colum) {
        this.headers_ = this.headers;
      } else {
        const val_heads = JSON.parse(ema_colum).map((x) => x.value);
        this.headers_ = this.headers
          .filter((x) => !x.text)
          .concat(this.headers.filter((x) => val_heads.includes(x.value)));
      }
    },
    async updatePage() {},
    addFavorite({ coin_name }) {
      const list_coin = localStorage.getItem("COIN_FAVORITE");
      if (!list_coin) {
        localStorage.setItem("COIN_FAVORITE", JSON.stringify([coin_name]));
      } else {
        let arr = JSON.parse(list_coin);
        if (arr.includes(coin_name)) {
          arr = arr.filter((x) => x != coin_name);
        } else {
          arr.unshift(coin_name);
        }
        localStorage.setItem("COIN_FAVORITE", JSON.stringify(arr));
      }
      this.initialize();
    },
    handleSearch(text) {
      const searcher = new JsonSearch(this.data);
      this.data_table = searcher.query(text.trim());
    },
    checkFavorite({ coin_name }) {
      const list_coin = localStorage.getItem("COIN_FAVORITE");
      if (!list_coin) return false;
      return JSON.parse(list_coin).includes(coin_name);
    },
    clickFavorite() {
      this.check_favorite = !this.check_favorite;
      if (this.check_favorite) {
        const check_list_coin = localStorage.getItem("COIN_FAVORITE");
        if (!check_list_coin){
          this.data_table = []
          return;
        } 
        const list_coin = JSON.parse(check_list_coin);
        this.data_table = this.data.filter((x) =>
          list_coin.includes(x.coin_name)
        );
      } else {
        this.data_table = this.data;
      }
    },
    clickFilterColumn() {
      this.$refs.dialogColumn.open();
    },
    check1H() {
      return this.arrHeader.some((x) => this.field1H.includes(x));
    },
    check4H() {
      return this.arrHeader.some((x) => this.field4H.includes(x));
    },
    check24H() {
      return this.arrHeader.some((x) => this.field24H.includes(x));
    },
    colspan1h() {
      let count = 0;
      this.arrHeader.forEach((element) => {
        if (this.field1H.includes(element)) {
          count++;
        }
      });
      return count;
    },
    colspan4h() {
      let result = 0;
      this.arrHeader.forEach((element) => {
        if (this.field4H.includes(element)) {
          result++;
        }
      });
      return result;
    },
    colspan24h() {
      let result = 0;
      this.arrHeader.forEach((element) => {
        if (this.field24H.includes(element)) {
          result++;
        }
      });
      return result;
    },
  },
  computed: {
    listFields() {
      const list_header = this.headers.filter((x) => x.text);
      return list_header.map((x) => ({
        text: x.text,
        value: x.value,
      }));
    },
    arrHeader() {
      if (!this.headers_.length) {
        return [];
      }
      return this.headers_.filter((x) => x.text).map((x) => x.value);
    },
  },
};
</script>
<style>
@keyframes price-change {
  from {
    background-color: #4caf50;
  }
  to {
    background-color: black;
  }
}
.changed {
  animation-name: price-change;
  animation-duration: 2s;
}
.normal {
  color: white;
}
.color-red {
  color: #f44336;
}
.color-green {
  color: #4caf50;
}
.color-orange {
  color: #ff9800;
}
.color-favorite {
  color: #ff9800;
}

.theme--dark.v-data-table > .v-data-table__wrapper > table > thead > tr > th {
  color: white;
}
.border-table {
  border-left: 1px solid rgb(49, 49, 49);
  border-bottom: 1px solid rgb(49, 49, 49);

}
.custom_table_class {
  /* border-collapse: collapse; */
}
.v-data-table {
  background:url(@/assets/watermarked.png) no-repeat; 
  background-position: center;
}

</style>
