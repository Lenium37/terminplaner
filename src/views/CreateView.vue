<template>
  <a-form
    ref="formRef"
    name="dynamic_form_nest_item"
    :model="createTerminplanerForm"
    layout="vertical"
    @finish="onFinish"
  >

     <a-typography-title>Umfrage erstellen</a-typography-title>

     <a-form-item label="Titel" name="title"
          :rules="[{ required: true, message: 'Bitte einen Titel eingeben!' }]">
          <a-input v-model:value="createTerminplanerForm.title" />
      </a-form-item>

      <a-form-item label="Dein Name" name="name"
          :rules="[{ required: true, message: 'Bitte deinen Namen eingeben!' }]">
          <a-input v-model:value="createTerminplanerForm.name" />
      </a-form-item>

      <a-form-item label="Beschreibung" name="description">
          <a-textarea v-model:value="createTerminplanerForm.description" />
      </a-form-item>

      <a-form-item label="Ort" name="place">
          <a-input v-model:value="createTerminplanerForm.place" />
      </a-form-item>

    <a-space
      v-for="(appointment, index) in createTerminplanerForm.appointments"
      :key="appointment.id"
      style="display: flex; margin-bottom: 8px"
    >
      <a-form-item
        :name="['appointments', index, 'date']"
        :rules="{
          required: true,
          message: 'Datum fehlt',
        }"
      >
        <a-date-picker 
          v-model:value="appointment.date" 
          placeholder="Datum"
        />
      </a-form-item>

      <br/>

      <a-form-item
        :name="['appointments', index, 'time_start']"
        :rules="{
          required: true,
          message: 'Startuhrzeit fehlt',
        }"
      >
        <a-time-picker 
          v-model:value="appointment.time_start"
          format="HH:mm" 
          placeholder="Startzeit"
        />
      </a-form-item>

      <a-form-item
        :name="['appointments', index, 'time_end']"
      >
        <a-time-picker 
          v-model:value="appointment.time_end" 
          format="HH:mm"
          placeholder="Endzeit"
        />
      </a-form-item>
      <MinusCircleOutlined @click="delAppointment(appointment)" />
    </a-space>

    <a-form-item>
      <a-button type="dashed" block @click="addAppointment">
        <PlusOutlined />
        Termin hinzufügen
      </a-button>
    </a-form-item>

    <a-form-item>
      <a-button type="primary" html-type="submit">Erstellen</a-button>
    </a-form-item>
  </a-form>
</template>

<script lang="ts">
import { MinusCircleOutlined, PlusOutlined } from '@ant-design/icons-vue';
import { defineComponent, reactive, ref } from 'vue';
import type { FormInstance } from 'ant-design-vue';

interface Appointment {
  id: number;
  date: string;
  time_start: string;
  time_end: string;
}

export default defineComponent({
  components: {
    MinusCircleOutlined,
    PlusOutlined,
  },
  setup() {
    const formRef = ref<FormInstance>();
    const createTerminplanerForm = reactive<{ 
          title: string, 
          name: string, 
          description: string, 
          place: string, 
          appointments: Appointment[]
       }>({
          title: '',
          name: '',
          description: '',
          place: '',
          appointments: [],
    });

    const delAppointment = (item: Appointment) => {
      let index = createTerminplanerForm.appointments.indexOf(item);
      if (index !== -1) {
        createTerminplanerForm.appointments.splice(index, 1);
      }
    };
    const addAppointment = () => {
      createTerminplanerForm.appointments.push({
        date: '', // use Date from last entry if exists
        time_start: '', // use Time from last entry if exists
        time_end: '',
        id: Date.now(),
      });
    };
    const onFinish = values => {
      console.log('Received values of form:', values);
      console.log('dynamicValidateForm.title:', createTerminplanerForm.title);
      console.log('dynamicValidateForm.appointments:', createTerminplanerForm.appointments);
    };
    return {
      formRef,
      createTerminplanerForm,
      onFinish,
      delAppointment,
      addAppointment,
    };
  },
});
</script>