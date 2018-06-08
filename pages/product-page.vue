<template>
  <v-layout column fill-height>
    <v-flex>

      <!-- Messages  -->
      <v-expansion-panel>
        <v-expansion-panel-content>
          <div slot="header">Message</div>
          <v-card>
            <v-card-text>
              <v-text-field
                v-for="(message, i) in messages"
                v-model="messages[i]"
                :key="i"
                :id="'message_' + i"
                :label="i.toUpperCase()"
              ></v-text-field>
            </v-card-text>
          </v-card>
        </v-expansion-panel-content>

        <!-- References  -->
        <v-expansion-panel-content>
          <div slot="header">References</div>
          <v-card>
            <v-card-text>
              <v-text-field
                id="references"
                multi-line
              ></v-text-field>
            </v-card-text>
          </v-card>
        </v-expansion-panel-content>
      </v-expansion-panel>

      <v-btn
        color="primary"
        @click.prevent="generateCode"
        >Generate code</v-btn>

    </v-flex>
    <ScriptOutput :output-string="finalScript" :show-script="showScript" @close="showScript=false" />
  </v-layout>
</template>

<script>
  import stripIndent from 'strip-indent'
  import ScriptOutput from '~/components/ScriptOutput'
  
  export default {
    data () {
      return {
        langages: ['fr'],
        messages: {
          us: '',
          fr: '',
          en: '',
          de: '',
          es: '',
          nl: ''
        },
        references: 'toto totot',
        showScript: false,
        finalScript: 'toto',
        isValid: true
      }
    },
    methods: {
      generateCode () {
        if (!this.messages || !this.references) {
          this.isValid = false
          return
        }

        var refsOutput = this.references.split(',').map((ref) => {
          return ref.trim()
        })

        this.isValid = true

        let script = `
        (function ($) {
          if (!$('body').hasClass('catalog-product-view')) return;

          var wording = ${this.messages};
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
        `
        this.finalScript = stripIndent(script).trim();
        this.showScript = true
      }
    },
    components: {
      ScriptOutput: ScriptOutput
    }
  }
</script>

<style scoped>
  .btn {
    margin: 20px 0 0;
  }
  .flex {
    flex-shrink: 0;
    flex-grow: 0;
  }
</style>
