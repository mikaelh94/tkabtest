<template>
  <div class="page-wrapper min-h-screen">
    <Back/>
    <section class="page-section flex flex-col justify-around py-4">
      <h1 class="title">
        Product Page
      </h1>
      <form action="" class="text-left w-1/2 mx-auto" @submit.prevent="generateCode">


        <div class="mb-4 flex flex-row justify-between">
          <label for="lang_us" class="label cursor-pointer">
            <input v-model="selectedLangs" type="checkbox" value="us" id="lang_us" class="input-checkbox">
            US
          </label>
          <label for="lang_fr" class="label cursor-pointer">
            <input v-model="selectedLangs" type="checkbox" value="fr" id="lang_fr" class="input-checkbox">
            FR
          </label>
          <label for="lang_en" class="label cursor-pointer">
            <input v-model="selectedLangs" type="checkbox" value="en" id="lang_en" class="input-checkbox">
            EN
          </label>
          <label for="lang_de" class="label cursor-pointer">
            <input v-model="selectedLangs" type="checkbox" value="de" id="lang_de" class="input-checkbox">
            DE
          </label>
          <label for="lang_es" class="label cursor-pointer">
            <input v-model="selectedLangs" type="checkbox" value="es" id="lang_es" class="input-checkbox">
            ES
          </label>
          <label for="lang_nl" class="label cursor-pointer">
            <input v-model="selectedLangs" type="checkbox" value="nl" id="lang_nl" class="input-checkbox">
            NL
          </label>
        </div>
        <div class="flex ">
          <div class="mb-4" v-for="(lang, index) in selectedLangs" :key="index">
            <label :for="'message-' + lang" class="label">
              Message {{lang}}
              <input v-model="message[lang]" type="text" :id="'message-' + lang" class="input-text">
            </label>
          </div>
        </div>

        <div class="mb-4">
          <label for="refs" class="label">
            Références (SKU)
            <textarea v-model="refs" id="refs" cols="30" rows="3" class="textarea">

            </textarea>
          </label>
        </div>


        <div class="mb-4">
          <input type="submit" value="Generate" class="btn cursor-pointer mt-2" :class="{ 'btn-error': !isValid }">
        </div>

      </form>
    </section>

    <section class="page-section p-8">
      <p v-if="output !== ''" class="text-left output-code border border-green-dark p-4">
        {{output}}
      </p>
    </section>
  </div>
</template>

<script>
import Back from '~/components/Back.vue'
import uuidv4 from 'uuid/v4';

export default {
  components: {
    Back: Back
  },
  data() {
    return {
      selectedLangs: [],
      refs: 'toto, titi, tutu',
      message: {
        us: '',
        fr: '',
        en: '',
        de: '',
        es: '',
        nl: '',
      },
      output: '',
      isValid: true
    }
  },
  methods: {
    generateCode() {
      if (!this.message || !this.refs) {
        this.isValid = false;
        return;
      }

      var refsOutput = this.refs.split(',').map((ref) => {
        return ref.trim();
      });

      this.isValid = true;
      this.output = `
        (function ($) {
          if (!$('body').hasClass('catalog-product-view')) return;

          var wording = ${this.message};
          var refs = ["${refsOutput.join('","')}"];

          $.each(dataLayer[0].page.product_lists, function(index, productList) {
            if (productList.list.listName !== 'cart-list') {
              var sku = productList.list.items[0].product.sku;
              if(sku && ref.indexOf(sku) >= 0){
                $(".product-additional-infos").prepend($('<p class="special-promo-label">' + wording.toUpperCase() + '</p>'));
              }
            }
          });

        })(jQuery);
      `;
    },
    setUid() {
      return uuidv4();
    }
  }
}
</script>

<style>
.output-code {
  white-space: pre;
  overflow: hidden;
  height: 100%;
  overflow-y: auto;
}
</style>
