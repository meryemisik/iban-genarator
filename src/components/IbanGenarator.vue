<template>
  <div class="iban">
    <div class="iban-generator">
      <div class="random-iban-result">
        <h2 class="random-iban-title">Random Iban Generator</h2>
      </div>
      <div>
        <h3 class="random-iban-current-iban">
          <span>{{ this.currentIban }}</span>
          <a-button class="random-iban-copy-icon" @click="copyIban"
            ><a-icon type="copy"
          /></a-button>
        </h3>
      </div>
      <div class="country-change">
        <a-select
          default-value="Germany"
          class="country-change-select"
          :size="size"
          @change="countryChange($event)"
        >
          <a-select-option
            v-for="i in countryList"
            :key="i.code"
            :label="i.country"
            :value="i.code"
          >
            {{ i.country }}
          </a-select-option>
        </a-select>
        <a-button
          class="country-change-button"
          @click="updateGeneratedIban($event)"
          >generate Iban</a-button
        >
      </div>
    </div>
  </div>
</template>
<script>
 import countriesCode from "../data/countries";
 import iban from "../data/ibanValidate";
export default {
  data: () => ({
    ibanResult: "",
    countryList: countriesCode,
    ibanLength: "",
    currentIban: "",
    ibanCode: "",
  }),
  mounted() {
    this.countryChange("DE");
  },

  methods: {
    copyIban() {
      const copyText = this.currentIban;
      const textArea = document.createElement("textarea");
      textArea.textContent = copyText;
      textArea.classList.add("textarea-input");
      document.body.append(textArea);
      textArea.select();
      document.execCommand("copy");
      this.$notification["success"]({
        message: "Copy successful",
      });
    },
    updateGeneratedIban(e) {
      e = this.ibanCode;
      this.countryChange(e);
    },
    countryChange(x) {
      this.ibanCode = x;
      this.countryList
        .filter((e) => e.code == x)
        .map((e) => {
          var nums = "0123456789";
          var letters = "ABCDEFGHIJKLMNOPRSTUVYZWXQ";
          var chars = "";
          var generatedIban = "";
          this.ibanLength = e.length - 2;
          var splitFormat = e.format.split("!");

          for (var k = 0; k < splitFormat.length; k++) {
            if (splitFormat.length != k + 1) {
              for (var i = 1; i <= splitFormat[k].replace(/^\D+/g, ""); i++) {
                if (splitFormat[k + 1][0] == "n") {
                  chars = nums;
                } else if (splitFormat[k + 1][0] == "c") {
                  chars = nums + letters;
                } else if (splitFormat[k + 1][0] == "a") {
                  chars = letters;
                }
                var randomNumber = Math.floor(Math.random() * 10);
                generatedIban += chars.substring(
                  randomNumber,
                  randomNumber + 1
                );
              }
            }
          }
          if (iban.isValid(e.code + generatedIban) == false) {
            this.countryChange(x);
          } else {
            this.currentIban = e.code + generatedIban;
            return this.currentIban;
          }
        });
    },
  },
};
</script>
<style lang="scss">
@import "../assets/scss/stil.scss";
</style>
