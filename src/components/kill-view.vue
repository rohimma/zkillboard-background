<template>
    <div class="kill-mail-preview">
        <img :src="lossSrc" />
        <img :src="userSrc" />
        <img :src="corpSrc" v-if="corpSrc != null" />
        <span :src="corpSrc" v-if="corpSrc == null" ></span>
        <img :src="allySrc" v-if="allySrc != null" />
        <span :src="allySrc" v-if="allySrc == null" ></span>
        <span class="total-value-txt">{{ zkillTotalValue }}</span>
    </div>
</template>

<script>
  export default {
    name: 'kill-view',
    props: ['data'],
    computed: {
      userSrc: function () {
        if (typeof this.data.package.killmail.victim.character !== 'undefined') {
          return this.data.package.killmail.victim.character.icon.href
        }

        return null
      },
      lossSrc: function () {
        if (typeof this.data.package.killmail.victim !== 'undefined') {
          return this.data.package.killmail.victim.shipType.icon.href
        }

        return null
      },
      corpSrc: function () {
        if (this.data.package.killmail.victim.corporation !== 'undefined') {
          return this.data.package.killmail.victim.corporation.icon.href
        }

        return 'https://image.eveonline.com/Character/1_64.jpg'
      },
      allySrc: function () {
        if (typeof this.data.package.killmail.victim.alliance !== 'undefined') {
          return this.data.package.killmail.victim.alliance.icon.href
        }

        return null
      },
      zkillTotalValue: function () {
        if (typeof this.data.package.zkb !== 'undefined' && typeof this.data.package.zkb.totalValue !== 'undefined') {
          var nStr = this.data.package.zkb.totalValue
          nStr += ''
          var x = nStr.split('.')
          var x1 = x[0]
          var x2 = x.length > 1 ? ',' + x[1] : ',00'
          var rgx = /(\d+)(\d{3})/

          while (rgx.test(x1)) {
            x1 = x1.replace(rgx, '$1' + '.' + '$2')
          }

          return x1 + x2
        }

        return null
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    .kill-mail-preview {
        display: grid;
        grid-template-columns: repeat(4, 32px) 1fr;
        grid-gap: 3px;
        margin-bottom: 3px;
        color: whitesmoke;
        text-align: center;
    }

    img {
        width: 32px;
    }

    .total-value-txt {
        text-align: right;
        color: whitesmoke;
    }
</style>
