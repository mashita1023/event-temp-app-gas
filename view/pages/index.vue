<template>
  <v-container>
    <Loading v-show="loading"></Loading>
    <div v-show="!loading">
      <v-row justify="center" align="center">
        <v-col cols="12" sm="8" md="6">
          <v-card class="pt-16 pb-16">
            <v-row>
              <v-col cols="3" />
              <v-col cols="6">
                <v-text-field v-model="name" label="名前" />
              </v-col>
              <v-col cols="3" />
            </v-row>
            <v-row>
              <v-col cols="1" />
              <v-col cols="2">
                <v-btn block @click="temp_dec">-</v-btn>
              </v-col>
              <v-col cols="6">
                <v-text-field v-model="temp" label="体温" />
              </v-col>
              <v-col cols="2">
                <v-btn block @click="temp_inc">+</v-btn>
              </v-col>
              <v-col cols="1" />
            </v-row>
            <v-row>
              <v-col cols="3"></v-col>
              <v-col cols="6">
                <v-select
                  v-model="condition"
                  :items="select"
                  @change="changeCondition"
                />
              </v-col>
              <v-col cols="3"></v-col>
            </v-row>
            <v-row v-show="isBadCondition">
              <v-col cols="3"></v-col>
              <v-col cols="6">
                <v-text-field v-model="remark" label="どこが悪いですか？" />
              </v-col>
              <v-col cols="3"></v-col>
            </v-row>
            <v-row>
              <v-col cols="4"></v-col>
              <v-col cols="4">
                感染者との接触
                <v-btn-toggle v-model="contact">
                  <v-btn>ある</v-btn>
                  <v-btn>ない</v-btn>
                </v-btn-toggle>
              </v-col>
              <v-col cols="4"></v-col>
            </v-row>
            <v-row align-content="center">
              <v-col cols="4"></v-col>
              <v-col cols="4">
                感染拡大地域訪問
                <v-btn-toggle v-model="visit">
                  <v-btn>ある</v-btn>
                  <v-btn>ない</v-btn>
                </v-btn-toggle>
              </v-col>
              <v-col cols="4"></v-col>
            </v-row>
            <v-row>
              <v-col cols="1" />
              <v-col cols="10">
                <v-btn block @click="submit">送信</v-btn>
              </v-col>
              <v-col cols="1"></v-col>
            </v-row>
          </v-card>
        </v-col>
      </v-row>
    </div>
  </v-container>
</template>

<script>
import axios from "axios";
import Loading from "@/components/Loading.vue";
export default {
  components: {
    Loading
  },
  data() {
    return {
      name: null,
      temp: 36.5,
      condition: "良い",
      contact: 1,
      visit: 1,
      isBadCondition: false,
      remark: "",
      select: ["良い", "普通", "悪い"],
      loading: false
    };
  },
  computed: {},
  methods: {
    temp_inc: function() {
      this.temp = (this.temp * 10 + 1) / 10;
    },
    temp_dec: function() {
      this.temp = (this.temp * 10 - 1) / 10;
    },
    changeCondition: function() {
      if (this.condition == "悪い") {
        this.isBadCondition = true;
      } else {
        this.isBadCondition = false;
        this.remark = "";
      }
    },
    refresh: function() {
      this.name = null;
      this.temp = 36.5;
      this.condition = "良い";
      this.contact = 1;
      this.visit = 1;
      this.isBadCondition = false;
      this.remark = "";
    },
    isCheck: function(num) {
      if (num == 0) {
        return "ある";
      } else {
        return "ない";
      }
    },
    submit: async function() {
      const CORS_PROXY = "https://event-temp-app.vercel.app/";
      const URL =
        "https://script.google.com/macros/s/AKfycbzlOlX-vzjjEmuSvdKGcJoGTgFof6AfklsFshy6IPyyR1W7FS48JHpON2g5YTItg-nhew/exec";

      try {
        if (this.name == null || this.name == "") {
          throw "名無しです";
        }
        var params = {
          name: this.name,
          temp: this.temp,
          condition: this.condition,
          contact: this.isCheck(this.contact),
          visit: this.isCheck(this.visit),
          remark: this.remark,
          crossDomain: true
        };

        await axios.get(URL, params);
        this.refresh();
      } catch (e) {
        console.log(e);
      }
    }
  }
};
</script>
