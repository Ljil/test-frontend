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
          :disabled="!selected.length"
      >Снять с должност{{ selected.length > 1 ? 'ей':'и'}}</v-btn>
    </v-card-actions>
    <v-data-table
        v-model="selected"
        item-key="id"
        :headers="headers"
        :items="data"
        :item-class="getRowColor"
        :items-per-page="5"
        show-select
        class="elevation-1"
        :search="search"
        :custom-filter="filterNames"
        multi-sort
    >
      <!-- Выбор всех -->
      <template v-slot:header.data-table-select>
        <!-- Выбор  всех (на всех страницах) -->
        <v-simple-checkbox color="success" @click="checkAll()" v-model="selected.length"></v-simple-checkbox>
      </template>

      <!-- выбор строки и активация чекбокса у уволенных -->
      <template v-slot:item.data-table-select="{item}">
        <v-simple-checkbox
            color="success"
            @click="check(item)"
            v-model="item.selected"
            v-if="!item.fireDate"
        ></v-simple-checkbox>
      </template>

      <!-- Редактирование Ставки:salary fraction -->
      <template v-slot:item.salary="{ item }">
        <v-edit-dialog :return-value.sync="item.salary">
          {{item.salary}}₽ ({{item.fraction}})%
          <template v-slot:input>
            <v-container>
              <v-row>
                <v-col>
                  <v-text-field
                      :disabled="item.fireDate"
                      pa-6
                      v-model="item.salary"
                      label="Ставка, руб"
                  ></v-text-field>
                </v-col>
                <v-col>
                  <v-text-field
                      :disabled="item.fireDate"
                      py-6
                      v-model="item.fraction"
                      label="Ставка, %"
                  ></v-text-field>
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

      <!-- Редактирование Базы:base -->
      <template v-slot:item.base="{ item }">
        <v-edit-dialog :return-value.sync="item.base">
          {{ item.base }}₽
          <template v-slot:input>
            <v-text-field :disabled="item.fireDate" v-model="item.base" label="База, руб"></v-text-field>
            <v-btn color="success" text>Отменить</v-btn>
            <v-btn color="success" text>Сохранить</v-btn>
          </template>
        </v-edit-dialog>
      </template>
      <!-- Редактирование Аванса:advance -->
      <template v-slot:item.advance="{ item }">
        <v-edit-dialog :return-value.sync="item.advance">
          {{item.advance}}₽
          <template v-slot:input>
            <v-text-field :disabled="item.fireDate" v-model="item.advance" label="Аванс, руб"></v-text-field>
            <v-btn color="success" text>Отменить</v-btn>
            <v-btn color="success" text>Сохранить</v-btn>
          </template>
        </v-edit-dialog>
      </template>
      <!-- Чекбокс почасовой -->
      <template v-slot:item.byHours="{ item }">
        <v-simple-checkbox v-model="item.byHours" :disabled="item.fireDate" color="success"></v-simple-checkbox>
      </template>
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
      allChecked:false,
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
    checkAll() {
      if(this.allChecked) {
        this.data.forEach(item => {
          item.selected = false
        })
        this.selected = []
      } else {
        this.data.forEach(item => {
          if(!item.fireDate) {
            item.selected = true
            this.selected.push(item)
          }
        })
      }
      this.allChecked = !this.allChecked
    },
    check(item) {
      //добавляет в массив выделенных (только если нет fireDate)
      if (item.fireDate !== null) return;
      const index = this.selected.indexOf(item);
      if (index > -1) {
        this.selected.splice(index, 1);
      } else {
        this.selected.push(item);
      }
    },
    async loadData() {
      this.pre_data = await axios.post("http://localhost:8000/api/", {
        query: `{
          getOccupations{
            id
            advance
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
    },
  },
};
</script>