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
      <v-checkbox v-model="fired" color="success" class="mx-2" label="Показывать уволенных"></v-checkbox>
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
        <v-edit-dialog pa-6 :return-value.sync="item.salary">
          {{item.salary}}₽ ({{item.fraction}})%
          <template v-slot:input>
            <v-text-field v-model="item.salary" label="Ставка, руб"></v-text-field>
            <v-text-field v-model="item.fraction" label="Ставка, %"></v-text-field>
            <v-btn text>Отменить</v-btn>
            <v-btn text>Сохранить</v-btn>
          </template>
        </v-edit-dialog>
      </template>

      <template v-slot:item.base="{ item }">
        <!-- Редактирование Базы:base -->
        <v-edit-dialog :return-value.sync="item.base">
          {{ item.base }}₽
          <template v-slot:input>
            <v-text-field v-model="item.base" label="База, руб"></v-text-field>
          </template>
        </v-edit-dialog>
      </template>

      <template v-slot:item.advance="{ item }">
        <!-- Редактирование Аванса:advance -->
        <v-edit-dialog :return-value.sync="item.advance">
          {{item.advance}}₽
          <template v-slot:input>
            <v-text-field v-model="item.advance" label="Аванс, руб"></v-text-field>
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
      data: [
        {
          name: "Джордж Вашингтон",
          companyName: 'ООО "Синергия"',
          positionName: "Первый президент США",
          hireDate: "1789-04-30",
          fireDate: "1797-03-04",
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: true,
        },
        {
          name: "Ричард I Львиное Сердце",
          companyName: 'ООО "Синергия"',
          positionName: "Король Англии",
          hireDate: "1189-01-01",
          fireDate: "1199-05-17",
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: true,
        },
        {
          name: "Джейсон Стейтем",
          companyName: 'ООО "Синергия"',
          positionName: "Бейкон",
          hireDate: "1998-09-01",
          fireDate: null,
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: false,
        },
        {
          name: "Тарантино К. Дж.",
          companyName: 'ООО "Синергия"',
          positionName: "Джимми",
          hireDate: "1994-04-15",
          fireDate: null,
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: false,
        },
        {
          name: "Камбербэтч Б.",
          companyName: 'ООО "Синергия"',
          positionName: "Смауг",
          hireDate: "1000-01-01",
          fireDate: "2941-10-09",
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: false,
        },
        {
          name: "Крузенштерн И. Ф.",
          companyName: 'ООО "Синергия"',
          positionName: "Человек и пароход",
          hireDate: "1770-11-08",
          fireDate: null,
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: false,
        },
        {
          name: "Бендер С. Р.",
          companyName: '"Planet Express"',
          positionName: "Робот-сгибальщик",
          hireDate: "2997-03-27",
          fireDate: null,
          salary: 1000,
          fraction: 100,
          base: 1000,
          advance: 1000,
          byHours: true,
        },
      ],
    };
  },
  methods: {
    filterNames(value, search, item) {
      return item.name.toLowerCase().indexOf(search.toLowerCase()) !== -1;
    },
    getRowColor(item) {
      return item.fireDate != null ? "red" : "";
    },
  },
};
</script>
