<template>
  <a-form
    ref="formRef"
    name="dynamic_form_nest_item"
    :model="createTerminplanerForm"
    layout="vertical"
    @finish="onFinish"
  >

     <a-typography-title>Terminplaner</a-typography-title>
     <a-typography-title :level="3">Umfrage erstellen</a-typography-title>

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
          format="DD.MM.YY"
          placeholder="Datum"
        />
      </a-form-item>

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

      <MinusCircleOutlined
        @click="delAppointment(appointment)" 
        style="margin-bottom: 24px;"
      />
    </a-space>

    <a-form-item>
      <a-button type="dashed" block @click="addAppointment">
        <PlusOutlined />
        Termin hinzufügen
      </a-button>
    </a-form-item>

    <a-form-item>
      <a-button type="primary" html-type="submit" style="float: right;">Erstellen</a-button>
    </a-form-item>
  </a-form>
</template>

<script lang="ts">
import { MinusCircleOutlined, PlusOutlined } from '@ant-design/icons-vue';
import { defineComponent, reactive, ref } from 'vue';
import type { FormInstance } from 'ant-design-vue';
import dayjs from 'dayjs';


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


    const defaultAppointment = (): Appointment[] => ([{
      date: dayjs(),
      time_start: null,
      time_end: null,
      id: Date.now()
    }]);

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
          appointments: defaultAppointment(),
    });

    const delAppointment = (item: Appointment) => {
      let index = createTerminplanerForm.appointments.indexOf(item);
      if (index !== -1 && createTerminplanerForm.appointments.length > 1) {
        createTerminplanerForm.appointments.splice(index, 1);
      }
    };

    const addAppointment = () => {
      let date_to_use = dayjs();//('00:00', 'dd.MM.YYYY');
      let time_start_to_use = dayjs('00:00', 'HH:mm');
      let time_end_to_use: dayjs.Dayjs = null;

      if(createTerminplanerForm.appointments.length > 0) {
        time_start_to_use = createTerminplanerForm.appointments[createTerminplanerForm.appointments.length - 1].time_start;
        time_end_to_use = createTerminplanerForm.appointments[createTerminplanerForm.appointments.length - 1].time_end;
      }

      createTerminplanerForm.appointments.push({
        date: date_to_use, // use Date from last entry if exists
        time_start: time_start_to_use,
        time_end: time_end_to_use,
        id: Date.now(),
      });
    };

    // const emptyAppointmentTime = (): AppointmentTime => ({
    //   hour: 0,
    //   minute: 0,
    // });


    const onFinish = values => {
      console.log('Received values of form:', values);
      console.log('dynamicValidateForm.title:', createTerminplanerForm.title);
      console.log('dynamicValidateForm.appointments:', createTerminplanerForm.appointments);

      for (let i = 0; i < createTerminplanerForm.appointments.length; i++) {
        let appointment = createTerminplanerForm.appointments[i];

        console.log('Appointment', i);
        console.log('Date:', appointment.date);
        console.log('Start time:', appointment.time_start);
        console.log('End time:', appointment.time_end);

      }
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