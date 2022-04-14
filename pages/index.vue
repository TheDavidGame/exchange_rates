<template>
  <div v-if="isLoading" class="modal-vue">
    <a-button
      type="primary"
      size="large"
      style="margin-bottom: 20px"
      @click="showModal = true"
      >Добавить курс</a-button
    >

    <a-row align="top" style="display: flex; flex-flow: row wrap">
      <div
        v-for="(obj, i) in arrayCur"
        :key="i"
        :span="12"
        style="margin-right: 20px"
      >
        <a-space>
          <a-col :span="12">
            <a-card
              :title="obj.Cur_Name"
              style="width: 300px; height: 150px; background-color: #ececec"
              class="textHeight"
            >
            </a-card>
          </a-col>
          <a-col :span="12">
            <a-button type="primary" size="large" @click="deleteCur(i)"
              >Удалить</a-button
            >
          </a-col>
        </a-space>
      </div>
    </a-row>

    <div v-if="showModal" class="overlay" @click="showModal = false"></div>

    <!-- modal -->
    <div v-if="showModal" class="modal">
      <div style="margin-bottom: 20px">
        <a-button class="NoButton" type="danger" @click="showModal = false"
          >Закрыть</a-button
        >
      </div>
      <a-row style="display: flex; flex-flow: row wrap">
        <div
          v-for="(obj, i) in arrayCurInModal"
          :key="i"
          style="margin-right: 20px"
        >
          <a-col :span="12">
            <a-space>
              <a-col :span="6">
                <a-card
                  :title="obj.Cur_Name"
                  style="width: 220px; height: 50px; background-color: #ececec"
                  class="textHeight"
                >
                </a-card>
              </a-col>
              <a-col :span="6">
                <a-button type="primary" size="large" @click="addNewCur(obj)"
                  >Добавить</a-button
                >
              </a-col>
            </a-space>
          </a-col>
        </div>
      </a-row>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'IndexPage',
  data() {
    return {
      isLoading: false,
      arrayCur: [],
      arrayCurInModal: [],
      showModal: false,
    }
  },

  computed: {},
  async mounted() {
    await this.validLocalStorage()
    await this.getApiCurModal()
    this.isLoading = true
  },
  methods: {
    validLocalStorage() {
      if (localStorage.getItem('Cur')) {
        try {
          this.arrayCur = JSON.parse(localStorage.getItem('Cur'))
        } catch (e) {
          localStorage.removeItem('Cur')
        }
      } else {
        axios
          .get('https://www.nbrb.by/api/exrates/rates?periodicity=0')
          .then((res) => {
            const parsed = JSON.stringify(res.data)
            localStorage.setItem('Cur', parsed)
            this.arrayCur = res.data
          })
        this.saveCur()
      }
    },
    getApiCurModal() {
      axios
        .get('https://www.nbrb.by/api/exrates/rates?periodicity=0')
        .then((res) => {
          this.arrayCurInModal = res.data
        })
    },
    saveCur() {
      const parsed = JSON.stringify(this.arrayCur)
      localStorage.setItem('Cur', parsed)
    },
    addNewCur(obj) {
      this.arrayCur.push(obj)
      this.saveCur()
    },
    deleteCur(index) {
      if (!this.arrayCur) {
        return
      }
      this.arrayCur.splice(index, 1)
      this.saveCur()
    },
  },
}
</script>
<style scss>
.textHeight {
  font-size: 20px;
}

/*modal window */

.modal-vue .overlay {
  position: fixed;
  z-index: 9998;
  top: 0;
  bottom: 50%;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
}

.modal-vue .modal {
  position: absolute;
  left: 20%;
  bottom: 30%;
  width: 600px;
  height: 400px;
  overflow: scroll;
  z-index: 9999;
  margin: 0 auto;
  padding: 20px 30px;
  background-color: #fff;
}

.modal-vue .close {
  position: absolute;
  top: 10px;
  right: 10px;
}
</style>
