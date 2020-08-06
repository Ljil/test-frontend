<template>
  <v-card>
    <v-card-title>
      Занимаемые долности
      <v-spacer></v-spacer>
      <v-text-field
        v-model="search"
        append-icon="mdi-magnify"
        label="Search"
        single-line
        hide-details
      ></v-text-field>
    </v-card-title>
    <v-card-actions>
      <v-spacer></v-spacer>
      <v-checkbox
        @click="showFired()"
        v-model="fired"
        color="success"
        class="mx-2"
        label="Показывать уволенных"
      ></v-checkbox>
      <v-btn color="success">Принять на должность</v-btn>
      <v-btn
        color="error"
        :disabled="selected.length === 0"
      >Снять с должност{{ selected.length > 1 ? 'ей':'и'}}</v-btn>
    </v-card-actions>
    <v-data-table
      v-model="selected"
      item-key="name"
      :headers="headers"
      :items="data"
      :items-per-page="5"
      show-select
      class="elevation-1"
      :search="search"
      :custom-filter="filterNames"
      multi-sort
    >
      <!-- Без выделения цветом -->
      <template v-slot:item.salary="{ item }">
        <!-- Редактирование Ставки:salary fraction -->
        <v-edit-dialog :return-value.sync="item.salary">
          {{item.salary}}₽ ({{item.fraction}})%
          <template v-slot:input>
            <v-container>
              <v-row>
                <v-col>
                  <v-text-field pa-6 v-model="item.salary" label="Ставка, руб"></v-text-field>
                </v-col>
                <v-col>
                  <v-text-field py-6 v-model="item.fraction" label="Ставка, %"></v-text-field>
                </v-col>
              </v-row>
              <v-row>
                <v-col>
                  <v-btn color="success" text>Отменить</v-btn>
                </v-col>
                <v-col>
                  <v-btn color="success" text>Сохранить</v-btn>
                </v-col>
              </v-row>
            </v-container>
          </template>
        </v-edit-dialog>
      </template>

      <template v-slot:item.base="{ item }">
        <!-- Редактирование Базы:base -->
        <v-edit-dialog :return-value.sync="item.base">
          {{ item.base }}₽
          <template v-slot:input>
            <v-text-field v-model="item.base" label="База, руб"></v-text-field>
            <v-btn color="success" text>Отменить</v-btn>
            <v-btn color="success" text>Сохранить</v-btn>
          </template>
        </v-edit-dialog>
      </template>

      <template v-slot:item.advance="{ item }">
        <!-- Редактирование Аванса:advance -->
        <v-edit-dialog :return-value.sync="item.advance">
          {{item.advance}}₽
          <template v-slot:input>
            <v-text-field v-model="item.advance" label="Аванс, руб"></v-text-field>
            <v-btn color="success" text>Отменить</v-btn>
            <v-btn color="success" text>Сохранить</v-btn>
          </template>
        </v-edit-dialog>
      </template>

      <template v-slot:item.byHours="{ item }">
        <v-simple-checkbox v-model="item.byHours" color="success"></v-simple-checkbox>
      </template>
      <!--  -->

      <!-- Выделение цветом -->
      <!-- <template v-slot:item="{ item }">
        <tr :style="{ backgroundColor: getRowColor(item) }">
          <td v-for="key in Object.keys(headers)" :key="key">
            <span v-if="String(headers[key].value) === 'byHours'">
              <v-simple-checkbox v-model="item.byHours" color="success"></v-simple-checkbox>
            </span>
            <span
              v-if="String(headers[key].value) === 'fraction'"
            >{{item.salary}}₽ ({{item.fraction}})%</span>
            <span v-if="String(headers[key].value) !== 'byHours'">{{ item[headers[key].value] }}</span>
          </td>
        </tr>
      </template>-->
      <!-- Выделение цветом -->
    </v-data-table>
  </v-card>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      search: "",
      fired: true,
      selected: [],
      headers: [
        { text: "Сотрудник", value: "name" },
        { text: "Компания", value: "companyName" },
        { text: "Должность", value: "positionName" },
        { text: "Дата приёма", value: "hireDate" },
        { text: "Дата увольнения", value: "fireDate" },
        { text: "Ставка", value: "salary" },
        { text: "База", value: "base" },
        { text: "Аванс", value: "advance" },
        { text: "Почасовая", value: "byHours" },
      ],
      data: [],
      pre_data: [],
    };
  },
  mounted() {
    this.loadData();
  },
  methods: {
    async loadData() {
      this.pre_data = await axios.post("http://localhost:8000/api/", {
        query: `{
          getOccupations{
            id
            name
            companyName
            positionName
            hireDate
            fireDate
            salary
            base
            fraction
            byHours
            }
            }`,
      });
      this.showFired();
    },
    filterNames(value, search, item) {
      return item.name.toLowerCase().indexOf(search.toLowerCase()) !== -1;
    },
    getRowColor(item) {
      return item.fireDate != null ? "red" : "";
    },
    showFired() {
      if (this.fired) {
        this.data = this.pre_data.data.data.getOccupations;
      } else {
        this.data = this.pre_data.data.data.getOccupations.filter(function (
          entry
        ) {
          return entry.fireDate === null;
        });
      }
      ``;
    },
  },
};
</script>