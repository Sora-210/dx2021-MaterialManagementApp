<template>
  <div>
    <StatusBar
      :allSection="allSection"
      :manySection="manySection"
      :fewSection="fewSection"
      :noneSection="noneSection"
      :coverSection="coverSection"
    />
    <CRow class="mb-5" :xs="{ cols: 1 }" :md="{ cols: 2 }" :xl="{ cols: 3 }">
      <CCol>
        <StatusCardB
          projectId="testProject"
          cameraName="Camera 1"
          cameraId="1"
        />
      </CCol>
    </CRow>
  </div>
</template>

<script>
import { ref } from 'vue'
import StatusCardB from '@/components/Status/StatusCardB.vue'
import StatusBar from '@/components/Status/StatusBar.vue'

import store from '@/store/index'
import api from '@/api'

export default {
  name: 'Dashboard',
  components: {
    StatusBar,
    StatusCardB,
  },
  setup() {
    const allSection = ref(0)
    const manySection = ref(0)
    const fewSection = ref(0)
    const noneSection = ref(0)
    const coverSection = ref(0)
    const list = ref([])

    return {
      allSection,
      manySection,
      fewSection,
      noneSection,
      coverSection,
      list,
    }
  },
  async mounted() {
    const option = {
      headers: {
        authorization: `Bearer ${store.getters.jwt}`,
      },
    }
    api
      .get(
        'https://api.sus-dx.sora210.net/testProject/inference/1/latest',
        option,
      )
      .then((res) => {
        let statusJson = res.data.data
        statusJson.forEach((value) => {
          console.log(value)
          this.list.push(value)
          value[1].forEach((value2) => {
            this.allSection++
            if (value2.status === 'many') {
              this.manySection++
            } else if (value2.status === 'few') {
              this.fewSection++
            } else if (value2.status === 'none') {
              this.noneSection++
            } else if (value2.status === 'cover') {
              this.coverSection++
            }
          })
        })
      })
  },
}
</script>
