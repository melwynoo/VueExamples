<template>
  <div id="countryList">
    <b-form-input v-model="filter" placeholder="Suchen"></b-form-input>
    <b-table
      striped
      :items="countryProvider"
      :fields="fields"
      :filter="filter"
      selectable
      :select-mode="selectMode"
      @row-selected="onRowSelected"
    >
      <template v-slot:cell(flag)="data">
        <img :src="data.value" width="50">
      </template>
      <template v-slot:cell(currencies)="cur">
        <dl>
          <template v-for="(x, key) in cur.value">
            <dt :key="'dt' + key">{{x.code}}</dt>
            <dd :key="'dd' + key">{{x.symbol}}</dd>
          </template>
        </dl>
      </template>
    </b-table>
    <b-modal id="modal-country-details" hide-footer>
      <template v-slot:modal-title>{{selected ? selected.name : 'Kein Land ausgewählt'}}</template>
      <div class="d-block">
        <CountryDetails :country="selected"/>
      </div>
      <b-button
        class="mt-3"
        variant="primary"
        block
        @click="$bvModal.hide('modal-country-details')"
      >Schließen</b-button>
    </b-modal>
  </div>
</template>

<script>
import CountryDetails from "./CountryDetails";

export default {
  name: "CountryList",
  components: { CountryDetails },
  data() {
    return {
      fields: [
        { key: "flag", label: "Flagge", sortable: true },
        { key: "name", label: "Name", sortable: true },
        { key: "nativeName", label: "Name in Landessprache", sortable: true },
        { key: "currencies", label: "Symbol", sortable: true }
      ],
      selectMode: "single",
      selected: {},
      filter: ""
    };
  },
  methods: {
    onRowSelected(items) {
      this.selected = items[0];
      if (this.selected) {
        this.$bvModal.show("modal-country-details");
      }
    },
    countryProvider(ctx) {
      return this.axios
        .get("https://restcountries.eu/rest/v2/all")
        .then(x => {
          return x.data || [];
        })
        .then(x => {
          if (ctx.filter.length > 0) {
            return x.filter(
              c =>
                c.name
                  .trim()
                  .toLowerCase()
                  .indexOf(ctx.filter.trim().toLowerCase()) >= 0
            );
          } else {
            return x;
          }
        })
        .then(x =>
          x.sort((a, b) => {
            if (a[ctx.sortBy] < b[ctx.sortBy]) return ctx.sortDesc ? 1 : -1;
            if (a[ctx.sortBy] > b[ctx.sortBy]) return ctx.sortDesc ? -1 : 1;
            return 0;
          })
        );
    }
  }
};
</script>

<style>
</style>
