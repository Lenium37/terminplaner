<template>
  <div v-if="active_terminplaner" class="terminplaner_select">
    <a-typography-title>{{ terminplaner_title }}</a-typography-title>
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

        <a-row type="flex">

          <a-col flex="auto">
            <a-typography-title :level="4">{{appointment.date}}</a-typography-title>

            <a-typography-title :level="5" v-if="appointment.time_end">{{appointment.time_start}} Uhr - {{appointment.time_end}} Uhr</a-typography-title>
            <a-typography-title :level="5" v-else>{{appointment.time_start}} Uhr</a-typography-title>

          </a-col>

          <a-col flex="1 1 30px" class="appointment_checkbox">
            <span class="selection_counter" :value="selection_count_live[index]">{{ selection_count_live[index] }}</span>
            <team-outlined />
            <!-- <a-divider type="vertical" /> -->
            <!-- <a-checkbox
              v-id="appointment.id"
              v-model="appointment.my_selection"
              :change="updateSelectionCount(appointment)"
              >
            </a-checkbox> -->
            <a-checkbox
              v-model="appointment.my_selection"
              @change="updateSelectionCount(appointment, index)"
              >
            </a-checkbox>
          </a-col>

        </a-row>
      </a-list>

      <a-button type="primary" html-type="submit" style="float: right;" @click="sendSelection">Absenden</a-button>
    </div>
  </div>
  <div v-else>
      <a-typography-title>Kein Terminplaner aktiv</a-typography-title>
      <!-- <router-link :to="/"></router-link> -->
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
    var selection_count_live: number[] = reactive([]);

    if (route.params["terminplaner"] != undefined) {
      active_terminplaner = true;
      let json_string: string = route.params["terminplaner"].toString();

      let parsed_form = JSON.parse(json_string);
      console.log(parsed_form);
      terminplaner_title = parsed_form.title;
      terminplaner_name = parsed_form.name;
      terminplaner_description = parsed_form.description;
      terminplaner_place = parsed_form.place;
      number_of_appointments = parsed_form.appointments.length;

      for (let i = 0; i < number_of_appointments; i++) {
        console.log("date:::", parsed_form.appointments[i].date)
        let time_end = "";
        if (parsed_form.appointments[i].time_end != undefined) {
          console.log("SETTING TIME END")
          time_end = dayjs(parsed_form.appointments[i].time_end).format("HH:mm");
        }

        appointments.push({
          "date": dayjs(parsed_form.appointments[i].date).format("dddd, DD MMMM"),
          "time_start": dayjs(parsed_form.appointments[i].time_start).format("HH:mm"),
          "time_end": time_end,
          "id": parsed_form.appointments[i].id,
          "my_selection": false,
        });
        console.log("added entry, ID:", parsed_form.appointments[i].id);

        selection_count.push(i*2);
        my_selection.push(0);
      }

      selection_count_live = selection_count;
    } else {
      router.push("/")
    }

    const updateSelectionCount = (appointment: AppointmentSelect, index: number) => {
      if (my_selection[index] == 1) {
        my_selection[index] = 0;
        selection_count_live[index]--;
      } else {
        my_selection[index] = 1;
        selection_count_live[index]++;
      }
    };

    const sendSelection = () => {
      router.push({
        name: "result",
        params: {
          "appointments": JSON.stringify(appointments),
          "selection_count": JSON.stringify(selection_count_live),
          "terminplaner_data": JSON.stringify({
              "title": terminplaner_title,
              "name": terminplaner_name,
              "description": terminplaner_description,
              "place": terminplaner_place,
          }),
          },
      });

    };

    return {
      formRef,
      active_terminplaner,
      terminplaner_title,
      terminplaner_name,
      terminplaner_description,
      terminplaner_place,
      appointments,
      number_of_appointments,
      my_selection,
      selection_count_live,
      updateSelectionCount,
      sendSelection
    };
  },

});
</script>