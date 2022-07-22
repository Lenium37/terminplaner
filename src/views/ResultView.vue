<template>
  <div v-if="active_terminplaner" class="terminplaner_select terminplaner_result">
    <a-typography-title>Ergebnis: {{ terminplaner_title }}</a-typography-title>
    <a-typography-title :level="3" class="terminplaner_name">Umfrage von: {{ terminplaner_name }}</a-typography-title>

    <div class="terminplaner_description">{{terminplaner_description}}</div>

    <div class="terminplaner_place" v-if="terminplaner_place">Ort: {{terminplaner_place}}</div>

    <div v-if="number_of_appointments === 1">{{number_of_appointments}} Termin:</div>
    <div v-else>{{number_of_appointments}} Termine:</div>

    <div class="appointments">
      <a-list
          v-for="(appointment, index) in appointments"
          :key="appointment.id"
          style="display: flex;"
        >

        <a-row type="flex" :class="{ 'most_selected' : selection_count[index] === max_selection}">

          <a-col flex="auto">
            <a-typography-title :level="4">{{appointment.date}}</a-typography-title>

            <a-typography-title :level="5" v-if="appointment.time_end">{{appointment.time_start}} Uhr - {{appointment.time_end}} Uhr</a-typography-title>
            <a-typography-title :level="5" v-else>{{appointment.time_start}} Uhr</a-typography-title>

          </a-col>

          <a-col flex="1 1 30px" class="appointment_checkbox">
            <span class="selection_counter" :value="selection_count[index]">{{ selection_count[index] }}</span>
            <team-outlined />
          </a-col>

        </a-row>
      </a-list>
    </div>
  </div>
  <div v-else>
      <a-typography-title>Kein Terminplaner aktiv</a-typography-title>
  </div>
  
</template>


<script lang="ts">
import { MinusCircleOutlined, PlusOutlined, TeamOutlined } from '@ant-design/icons-vue';
import { defineComponent, reactive, ref} from 'vue';
import type { FormInstance } from 'ant-design-vue';
import dayjs from 'dayjs';
import {useRoute, useRouter} from 'vue-router'
import { string } from 'vue-types';


interface AppointmentSelect {
  id: number;
  date: string;
  time_start: string;
  time_end: string;
  my_selection: boolean;
}

export default defineComponent({
  components: {
    MinusCircleOutlined,
    PlusOutlined,
    TeamOutlined
  },

  setup() {
    const formRef = ref<FormInstance>();
    const route = useRoute();
    const router = useRouter();


    var active_terminplaner = false;
    var terminplaner_title = "Titel";
    var terminplaner_name = "Unbekannt";
    var terminplaner_description = "Beschreibung";
    var terminplaner_place = "Ort";
    var appointments: AppointmentSelect[] = [];
    var number_of_appointments = 1;
    var my_selection: number[] = reactive([]);
    var selection_count: number[] = reactive([]);
    var max_selection = 0;
    console.log(route.params)

    if (route.params["appointments"] != undefined &&
        route.params["selection_count"] != undefined &&
        route.params["terminplaner_data"] != undefined) {
      active_terminplaner = true;

      appointments = JSON.parse(route.params["appointments"].toString());      
      selection_count = JSON.parse(route.params["selection_count"].toString());
      let terminplaner_data = JSON.parse(route.params["terminplaner_data"].toString());

      terminplaner_title = terminplaner_data.title;
      terminplaner_name = terminplaner_data.name;
      terminplaner_description = terminplaner_data.description;
      terminplaner_place = terminplaner_data.place;
      number_of_appointments = appointments.length;

      for (let i = 0; i < number_of_appointments; i++) {
        max_selection = Math.max(selection_count[i], max_selection);
      }
      console.log(max_selection);

    } else {
      router.push("/")
    }

    return {
      active_terminplaner,
      terminplaner_title,
      terminplaner_name,
      terminplaner_description,
      terminplaner_place,
      appointments,
      number_of_appointments,
      selection_count,
      max_selection,
    };
  },

});
</script>