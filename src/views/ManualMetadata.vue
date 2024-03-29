<template>
  <div class="manual--meta__container my-12">
    <div class="manual--meta md:w-3/5 w-11/12 mr-auto ml-auto py-6">
      <h2 class="text-3xl font-bold dark:text-white">
        Enter metadata manually
      </h2>
      <p class="mt-4 mb-8 text-gray-700 dark:text-gray-400">
        Modify the metadata according to your liking.
      </p>
      <ManualEach
        v-for="(option, id) in getManualOptions"
        :key="id"
        :option="option"
        :enableNormalize="option.enableNormalize"
        :index="id"
      />
      <div class="submit--btn text-center mt-14">
        <button
          type="button"
          class="py-4 px-12 rounded-md md:text-xl font-semibold strip-button"
          :disabled="isButtonDisabled"
          @click="handleNextPage()"
        >
          Set manual metadata
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import ManualEach from "@/components/ManualMetaEach";
import manualOptions from "@/static/manual.js";

export default {
  name: "ManualMeta",
  props: {
    videoId: {
      type: String,
    },
  },
  components: {
    ManualEach,
  },
  data() {
    return {
      enteredData: {},
      isDataValid: false,
    };
  },
  methods: {
    validateDataEntered: function () {
      /**
       * Check if the entered Data is valid
       *
       * We need to check if all the data in the
       * entered data is valid or not
       */
      this.isDataValid = Object.values(this.enteredData).every(
        (el) => el.valid
      );
    },
    buildMetaObject: function () {
      /**
       * Build a meta object based on the details entered
       * by the user.
       */
      const metaDetails = {
        time: 0,
      };
      Object.entries(this.enteredData).forEach((el) => {
        const key = el[0];
        const value = el[1];

        metaDetails[key] = value.value;
      });

      // Convert the number to int
      metaDetails["number"] = Number(metaDetails["number"]);

      return metaDetails;
    },
    handleNextPage: function () {
      const linkDetails = {
        name: "Format",
        params: {
          metaDetails: this.buildMetaObject(),
        },
        query: {
          videoId: this.$route.query.videoId,
        },
      };

      this.$router.push(linkDetails);
    },
  },
  computed: {
    getManualOptions() {
      return manualOptions;
    },
    isButtonDisabled() {
      /**
       * Check if all the objects have a valid
       * value in the enteredData object
       */
      return !this.isDataValid;
    },
  },
  created() {
    // Update the enteredData object with proper keys
    manualOptions.forEach((el) => {
      this.enteredData[el.attrName] = {
        value: el.default,
        valid: el.skippingAllowed,
      };
    });
  },
};
</script>

<style lang="scss" scoped>
.submit--btn {
  button {
    @extend .button-primary;
  }
}
</style>
