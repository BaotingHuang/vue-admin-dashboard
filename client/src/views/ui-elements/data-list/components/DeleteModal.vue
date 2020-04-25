<template>
  <b-modal
    centered
    v-model="isModalShow"
    content-class="rounded-xl"
    header-class="border-bottom-0"
    footer-class="border-top-0"
    title="提示"
    @hide="handleCancel"
  >
    <div class="d-flex align-items-center">
      <b-icon-exclamation-circle-fill variant="warning" class="mr-3 text-xl" />
      <span>确认要删除选中的交易记录吗?</span>
    </div>

    <template v-slot:modal-footer>
      <b-button
        class="d-flex align-items-center"
        variant="info"
        :disabled="isBtnDisabled"
        @click="handleDelete"
      >
        <b-spinner class="mr-1" small v-if="showSpinner"></b-spinner>
        <span>确认</span>
      </b-button>
      <b-button variant="outline-danger" @click="handleCancel()">
        <span>取消</span>
      </b-button>
    </template>
  </b-modal>
</template>

<script>
import { BIconExclamationCircleFill } from 'bootstrap-vue'
import { deleteTransaction, deleteManyTransaction } from '@/api/transactions'

export default {
  components: {
    BIconExclamationCircleFill
  },
  props: {
    ids: {
      type: Array,
      default: function () {
        return []
      }
    },
    modalShow: {
      type: Boolean,
      required: true
    }
  },
  data() {
    return {
      isModalShow: this.modalShow,
      isBtnDisabled: false,
      showSpinner: false
    }
  },
  watch: {
    modalShow(val) {
      this.isModalShow = val
    }
  },
  methods: {
    async handleDelete() {
      try {
        this.isBtnDisabled = true
        this.showSpinner = true

        if (this.ids.length === 1) {
          await deleteTransaction(this.ids)
        } else {
          await deleteManyTransaction(this.ids)
        }

        this.$bvModal.hide()

        this.handleShowAlert({
          variant: 'success',
          message: '删除成功'
        })
        this.handleRefresh()
      } catch (err) {
        this.handleShowAlert({
          variant: 'danger',
          message: '删除失败'
        })
      } finally {
        this.isBtnDisabled = false
        this.showSpinner = false
      }
    },
    handleCancel() {
      this.$emit('cancel')
    }
  }
}
</script>