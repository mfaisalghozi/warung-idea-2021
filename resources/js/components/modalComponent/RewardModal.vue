<template>
  <div class="modal-mask">
    <div class="modal-wrapper">
      <div class="modal-container">
        <div class="modal-header">
          <h5 class="modal-title">
            <b>Pilih Jumlah Donasimu</b>
          </h5>
          <button @click="closeModal" type="button" class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-scroll">
          <div class="modal-non-reward">
            <div class="text-label">Donasi Sukarela</div>
            <form @submit.prevent="donateAmount">
              <input
                v-model="amountDonation"
                id="amount"
                class="input-amount"
                type="text"
                name="amount"
              >
              <a @click="donateAmount" class="btn-donate" type="submit">
                Donate!
              </a>
            </form>
          </div>
          <div v-if="checkRegexAlphabet || checkAmountInvalid" class="text-error">Please Input Valid Number!</div>
          <div v-for="(d, idx) in listData" :key="idx">
            <router-link :to="{path: '/checkout', query: {campaignId: campaignId, totalAmount: d.amount, rewardName: d.title }}">
              <div class="reward-card-container">
                <div class="reward-price">
                  <div class="reward-image">
                  </div>
                  <div class="reward-text-price">
                    Rp {{ formatMoney(d.amount) }}
                  </div>
                </div>
                <div class="reward-title">
                  {{ d.title }}
                </div>
                <div class="reward-desc">
                  {{ d.description }}
                </div>
              </div>
            </router-link>
            <div
              v-if="checkUserOwner || isUserCollaborator"
              class="button-wrap"
            >
              <a :href="`/rewards/edit/${campaignId}/${d.id}`" class="btn btn-edit">
                Edit
              </a>
              <a @click="deleteReward(d.id)" class="btn btn-delete">
                Delete
              </a>
            </div>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RewardModal',
  props: {
    campaignId: {
      type: Number,
      default: 1,
    },
    userId: {
      type: Number,
      default: 1
    },
    ownerId: {
      type: Number,
      default: 1
    },
    isUserCollaborator: {
      type: Boolean,
      default: false,
    }
  },
  data: () => {
    return {
      listData: [],
      amountDonation: '0',
      realListData: []
    }
  },
  async created() {
    // this.generateMockData()
    await this.fetchingModalListData()
    if(this.listData.length <= 0) {
      await this.generateMockData()
    }
  },
  computed: {
    amountDonationToNumber () {
      return  parseInt(this.amountDonation)
    },
    checkRegexAlphabet () {
      const pattern = /^[0-9]*$/g
      return !pattern.test(this.amountDonation)
    },
    checkAmountInvalid () {
      return  this.amountDonationToNumber < 0
    },
    checkUserOwner() {
      // return parseInt(this.ownerId) === userId
      return parseInt(this.ownerId) === this.userId
    },
  },
  methods: {
    formatMoney(money) {
      const moneyTemp = money ? parseInt(money) : 10000
      const formatter = new Intl.NumberFormat('en-ID', {
        style: 'currency',
        currency: 'IDR'
      }).format(moneyTemp)
      .replace(/[IDR]/gi, '')
      .replace(/(\.+\d{2})/, '')
      .replace(/,/g, '.')
      .trimLeft()
      return formatter
    },
    generateMockData () {
      let mockData = []
      for(let x=1;x<=5;x++){
        const tempObj = {
          id: x,
          title: 'Paket Sumbangan Donasi ' + x,
          amount: x + '00000',
          rewardPrice: 'Rp' + x + '00.000',
          description: 'Lorem ipsum dolor sit amet consectetur adipisicing elit. Id illum sunt, temporibus unde aut veniam repellendus. Quo voluptatum ad praesentium.'
        }
        mockData.push(tempObj)
      }
      this.listData = mockData
    },
    async fetchingModalListData () {
      const campaignId = this.campaignId
      await this.$store
        .dispatch('getRewardsByCampaignId', campaignId)
        .then(res => {
          if(res.success && res.data && res.data.length > 0) {
            this.listData = res.data
          }
        })
        .catch(err => {
          console.error(err)
        })
    },
    closeModal () {
      this.$emit('close')
    },
    donateAmount () {
      if( this.checkRegexAlphabet || this.checkAmountInvalid || this.amountDonationToNumber === 0) {
        console.log('Invalid Donate Amount')
        return
      }

      const totalAmount = this.amountDonationToNumber
      this.$router.push({
        name: 'CheckoutPage',
        params: {
          totalAmount: totalAmount,
          campaignId: this.campaignId,
        }
      })
    },
    deleteReward(rewardId) {
      this.$store
        .dispatch('deleteRewards', rewardId)
        .then(res => {
          this.$router.go(0)
        })
        .catch(err => {
          console.error(err)
        })
    }
  }
}
</script>

<style lang="less" scoped>
.modal-mask {
    position: absolute;
    position: fixed;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    margin: auto;
    text-align: center;
    width: fit-content;
    height: fit-content;
    padding: 2rem;
    border-radius: 1rem;
    box-shadow: 0 5px 5px rgba(0, 0, 0, 0.2);
    background: #FFF;
    z-index: 999;
    transform: none;

  .modal-wrapper {
    display: flex;
    flex-direction: column;

    .modal-container {
      .modal-scroll {
        width: 700px;
        height: 700px;
        overflow: auto;

        &::-webkit-scrollbar {
          display: none;
        }

      }

      div > a {
        text-decoration: none;
        color: black;
      }

      .text-error {
        color: red;
      }

      .modal-non-reward {
        display: flex;
        flex-direction: row;
        padding: 10px;
        border-radius: 10px;
        margin: 30px 1rem;
        box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;
        justify-content: center;
        align-items: center;

        .text-label {
          margin: 0 10px;
          font-weight: bold;
          letter-spacing: 1px;
        }

        .input-amount {
          margin: 0 10px;
          width: 300px;
        }

        a {
          border: 1px solid black;
          border-radius: 10px;
          margin-left: .5rem;
          text-decoration: none;
          color: black;
          min-width: 5rem;

          &:hover {
            color: white;
            background-color: green;
          }
        }
      }

      .button-wrap {
          margin: 1rem 0;
          text-align: center;

          .btn {
            border: 1px solid black;
            border-radius: 10px;
            margin-left: .5rem;
            &-delete:hover {
              background-color: red;
              color: white
            }
            &-edit:hover {
              background-color: green;
              color: white;
            }
          }
        }

      .reward-card-container {
        text-align: left;
        display: flex;
        flex-direction: column;
        padding: 10px;
        border-radius: 10px;
        margin: 2rem 1rem;
        transition: 1s;
        box-shadow: rgba(0, 0, 0, 0.24) 0px 3px 8px;


        &:hover {
          margin: 3rem 1rem;
          box-shadow: rgba(50, 50, 93, 0.25) 0px 50px 100px -20px, rgba(0, 0, 0, 0.3) 0px 30px 60px -30px;
        }

        .reward-price {
          display: flex;
          flex-direction: row;
          align-items: center;

          .reward-image {
            background-color: black;
            width: 30px;
            height: 30px;
            border-radius: 50%;
            margin-right: 20px;
          }

          .reward-text-price {
            font-size: 25px;
            font-weight: bold;
          }
        }

        .reward-title {
          font-size: 20px;
          font-weight: bold;
        }

        .reward-desc {
          font-size: 15px;
        }
      }
    }
  }

}


</style>
