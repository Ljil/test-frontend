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
      <v-btn color="success">Снять с должности</v-btn>
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
    >
      <template v-slot:item.salary="{ item }">{{item.salary}}₽ ({{item.fraction}})%</template>
      <template v-slot:item.byHours="{ item }">
        <v-simple-checkbox v-model="item.byHours" color="success" disabled></v-simple-checkbox>
      </template>
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
  },
};
</script>
