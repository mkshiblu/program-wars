<template>
  <div>
    <div class="modal-dialog modal-lg" role="document" data-backdrop="static" data-keyboard="false">
      <div class="modal-content" style="border-radius: 30px">
        <div class="modal-header" style="border: none"> <button type="button" class="close" data-dismiss="modal" aria-label="Close" @click="overclockCanceled">
          <span aria-hidden="true">&times;</span>
        </button>
          <h3 class="modal-title">Overclock</h3>
        </div>
        <div class="modal-body">
          <div class="container col-lg-12">
            <div class="row">
              <p style="font-size: 24px">Do you want to use the Overclock card to double your current score?</p>
              <br>
            </div>
          </div>
        </div>
        <div class="modal-footer" style="border: none">
          <button class="btn btn-default" @click="discardOverclock" data-dismiss="modal" style="float: right; margin: 5px;" :style="hideButton">Discard OC Card</button>
          <button class="btn btn-default" @click="useClicked()" :disabled="checkUse"><b>Use</b></button>
          <button type="button" class="btn btn-default" data-dismiss="modal" style="margin: 5px;" @click="overclockCanceled">Cancel</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
  /* eslint-disable no-undef */

  import { bus } from '../../SharedComponents/Bus.vue'
  import {mapGetters, mapMutations, mapActions} from 'vuex'
export default {
    props: ['players'],

    methods: {
      /**
       * These mapping functions map local functions to the vuex functions or state.
       */
      ...mapActions([
        'playerTookTurn',
        'turn'
      ]),
      ...mapGetters([
        'getActiveCard',
        'getCurrentPlayer',
        'getTutorialState'
      ]),
      ...mapMutations([
        'increaseFactIndex',
        'discardSelectedCard',
        'giveOverclock'
      ]),
      overclockCanceled () {
        bus.$emit('hackCanceled')
      },
      discardOverclock () {
        if (this.getActiveCard() !== undefined) {
          this.discardSelectedCard()
          this.playerTookTurn()
          this.turn(true)
        }
      },
      useClicked () {
        let player = this.getCurrentPlayer()
        this.giveOverclock(player.id)
        $('.overclock').modal('hide')
        if (this.getTutorialState()) {
          bus.$emit('cardPlayed')
          this.increaseFactIndex()
        }
        this.playerTookTurn()
        this.turn(true)
        bus.$emit('alterTipBox')
      }

    },
    computed: {
      hideButton () {
        let activeCard = this.getActiveCard()
        if (activeCard !== undefined) {
          if (activeCard.type === 'OVERCLOCK') {
            return 'display: block'
          } else {
            return 'display: none'
          }
        } else {
          return 'display: none'
        }
      },
      checkUse () {
        return (this.getCurrentPlayer().hasOverclock)
      }
    },
    created () {
      $('.overclock').modal({
        backdrop: 'static',
        keyboard: false
      })
    }

  }

</script>

<style scoped>
  .stacks {
    width: 120px;
    border: 1px solid black;
  }
</style>
