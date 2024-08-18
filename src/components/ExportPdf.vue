<template>
  <CustomButtons btn-type="primary" @click="exportPdf" style="margin-bottom: 10px">
    Export PDF
  </CustomButtons>
</template>

<script>
import CustomButtons from './CustomButtons.vue'
export default {
  components: {
    CustomButtons
  },
  props: {
    resumeFormat: {
      type: String
    }
  },
  methods: {
    exportPdf() {
      const unit = this.resumeFormat == 'a4' ? 'mm' : 'in'

      const pdfConfig = {
        margin: 0,
        filename: 'resume',
        jsPDF: {
          unit: unit,
          format: this.resumeFormat,
          orientation: 'portrait'
        }
      }

      const resume = document.getElementById('resume')
      // eslint-disable-next-line no-undef
      html2pdf().set(pdfConfig).from(resume).save()
    }
  }
}
</script>

<style lang="scss" scoped></style>
