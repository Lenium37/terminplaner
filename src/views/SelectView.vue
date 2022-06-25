<template>
 <!-- <a-space
      v-for="(appointment, index) in createTerminplanerForm.appointments"
      :key="appointment.id"
      style="display: flex; margin-bottom: 8px"
    >
    </a-space> -->
  
</template>

<script lang="ts">
import { MinusCircleOutlined, PlusOutlined } from '@ant-design/icons-vue';
import { defineComponent, reactive, ref } from 'vue';
import type { FormInstance } from 'ant-design-vue';
import dayjs from 'dayjs';
import {useRoute} from 'vue-router'

interface Appointment {
  id: number;
  date: dayjs.Dayjs;
  time_start: dayjs.Dayjs;
  time_end: dayjs.Dayjs;
}

export default defineComponent({
  components: {
    MinusCircleOutlined,
    PlusOutlined,
  },

  setup() {
    const formRef = ref<FormInstance>();
    const route = useRoute();

    console.log("Welcome")
    console.log(route.params)
    let json_string: string = route.params["appointments"].toString();

    let parsed = JSON.parse(json_string);
    console.log(parsed);

    return {
      formRef
    };
  },
});
</script>