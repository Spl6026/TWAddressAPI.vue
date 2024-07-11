<script setup>
import CustomSelect from '@/components/select.vue';
</script>

<template>
  縣市:<CustomSelect :options="countyOptions" @selectedText="getCountySelectedText"
                @selectedValue="getCountySelected"></CustomSelect>
  鄉鎮市區:<CustomSelect :options="townOptions" @selectedText="getTownSelectedText"
                @selectedValue="getTownSelected"></CustomSelect>
  村里:<CustomSelect :options="villageOptions" @selectedText="getVillageSelectedText"
                @selectedValue="getVillageSelected"></CustomSelect>
  路:<CustomSelect :options="roadOptions" @selectedValue="getRoadSelected"></CustomSelect>
</template>
<script>
import axios from "axios";
import X2JS from "x2js";

const x2js = new X2JS;
export default {
  name: 'countyInput',
  props: {
    address: {
      type: String,
      default: ''
    }
  },
  data() {
    return {
      county: [],
      countyOptions: [],
      countySelected: '',
      countySelectedText: '',
      town: [],
      townOptions: [],
      townSelected: '',
      townSelectedText: '',
      village: [],
      villageOptions: [],
      villageSelected: '',
      villageSelectedText: '',
      road: [],
      roadOptions: [],
      roadSelected: ''
    };
  },
  created() {
    this.handleCounty();
  },
  methods: {
    async handleCounty() {
      let json = await this.loadJSON('https://api.nlsc.gov.tw/other/ListCounty')
      this.county = json.countyItems.countyItem
      this.countyOptions = this.handleOptions(this.county, 'countyname', 'countycode')
    },
    async handleTown() {
      let json = await this.loadJSON(`https://api.nlsc.gov.tw/other/ListTown1/${this.countySelected}`)
      this.town = json
      this.townOptions = this.handleOptions(this.town, 'townname', 'towncode01')
    },
    async handleVillage() {
      let json = await this.loadJSON(`https://api.nlsc.gov.tw/other/ListVillage/${this.countySelected}/${this.townSelected}`)
      this.village = json.villages.village
      this.villageOptions = this.handleOptions(this.village, 'villageName', 'villageId')
    },
    async handleRoad() {
      let json = await this.loadJSON(`https://api.nlsc.gov.tw/idc/ListRoadM/${this.countySelected}/${this.townSelected}`)
      this.road = json.roads.road
      this.roadOptions = this.handleOptions(this.road, 'name', 'name')
    },
    async loadJSON(url) {
      try {
        let response = await axios.get(url);
        let json;
        if (typeof response.data === "object") {
          json = response.data;
        } else {
          json = x2js.xml2js(response.data);
        }
        return json;
      } catch (error) {
        console.error(error);
        throw error;
      }
    },
    handleOptions(json, name, id) {
      let array = []
      json.forEach((item) => {
        let json = {
          'text': item[name],
          'value': item[id]
        }
        array.push(json)
      })
      return array
    },
    getCountySelected(selected) {
      this.countySelected = selected
      this.handleTown()
    },
    getCountySelectedText(selected) {
      this.countySelectedText = selected
    },
    getTownSelected(selected) {
      this.townSelected = selected
      this.handleVillage()
      this.handleRoad()
    },
    getTownSelectedText(selected) {
      this.townSelectedText = selected
    },
    getVillageSelected(selected) {
      this.villageSelected = selected
    },
    getVillageSelectedText(selected) {
      this.villageSelectedText = selected
    },
    getRoadSelected(selected) {
      this.roadSelected = selected
      this.getAddress()
    },
    getAddress() {
      const address = `${this.countySelectedText}${this.townSelectedText}${this.villageSelectedText}${this.roadSelected}`;
      this.$emit('update:address', address);
      return address;
    }
  }
}
</script>

<style scoped>
</style>