<template>
  <div class="upload-container">
    <el-upload
      ref="upload"
      :action="action"
      :headers="headers"
      :multiple="false"
      :limit="1"
      :file-list="fileList"
      :disabled="disabled"
      :before-upload="beforeUpload"
      :on-success="onSuccess"
      :on-error="onError"
      :on-remove="onRemove"
      :on-exceed="onExceed"
      drag
      show-file-list
      accept="application/epub+zip"
      class="image-upload"
    >
      <i class="el-icon-upload" />
      <div v-if="fileList.length === 0" class="el-upload__text">请将电子书拖入或<em>点击上传</em></div>
      <div v-else class="el-upload__text">电子书已上传</div>
    </el-upload>
  </div>
</template>

<script>
import { mapGetters } from 'vuex'

export default {
  props: {
    fileList: {
      type: Array,
      default() {
        return []
      }
    },
    disabled: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      action: `${process.env.VUE_APP_BASE_API}/book/upload`
    }
  },
  computed: {
    ...mapGetters(['token']),
    headers() {
      return {
        Authorization: `Bearer ${this.token}`
      }
    }
  },
  methods: {
    beforeUpload(file) {
      this.$emit('beforeUpload', file)
    },
    onSuccess(response, file) {
      console.log('onSuccess', response, file)
      const { code, msg } = response
      if (code !== 0) {
        this.$message({
          message: msg || '上传失败',
          type: 'error'
        })
        this.$emit('onError', file)
        return
      }
      this.$message({
        message: msg || '上传成功',
        type: 'success'
      })
      this.$emit('onSuccess', response)
    },
    onError(err) {
      const errMsg = err.message && JSON.parse(err.message)
      this.$message({
        message: (errMsg && errMsg.msg && `上传失败，失败原因：${errMsg.msg}`) || '上传失败',
        type: 'error'
      })
    },
    onRemove() {
      this.$message({
        message: '电子书删除成功',
        type: 'success'
      })
      this.$emit('onRemove')
    },
    onExceed() {
      this.$message({
        message: '每次只能上传一本电子书',
        type: 'warning'
      })
    }
  }
}
</script>

<style lang="scss" scoped>
</style>
