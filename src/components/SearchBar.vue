<template>
  <div id="searchbar">
    <SimpleTypeahead
      :items="finalList"
      :localStorageList="localStorageList"
      :placeholder="searchbarHolder"
      :minInputLength="minInputLength"
      @selectItem="selectItem"
      @onInput="onInput"
      @onBlur="onBlur"
      :itemProjection="
        (item) => {
          return item;
        }
      "
    >
    </SimpleTypeahead>
    <img src="../assets/searchIcon.svg" @click="searchDataInDatabase()" />
  </div>
</template>

<script>
// import "vue3-simple-typeahead/dist/vue3-simple-typeahead.css";
import SimpleTypeahead from "./vue3-simple-typeahead.vue";

export default {
  name: "SearchBar",
  components: {
    SimpleTypeahead
  },
  emits: ["productID"], 
  created() {
    this.filtered = this.list;
    this.refreshLocalStorageList();
  },
  data() {
    return {
      LOCAL_STORAGE_KEY: "searchHistory",
      searchbarHolder: "Type here...",
      minInputLength: 1,

      list: [ //最剛開始預設的 List
        "Abyss",
        "Blood",
        "Bubble",
        "Control",
        "Dark",
        "Earth",
        "Explosion",
        "Fire",
        "Frost",
        "Ice",
        "Heart",
        "Light",
        "Lightning",
        "Love",
        "Magic",
        "Mind",
        "Miracle",
        "Music",
        "Posion",
        "Shadow",
        "Thunder",
        "Water",
        "Wind",
      ],
      filtered: [], // 搜尋欄位打的東西
      localStorageList: [], //LocalStorage 裡存的所有東西
      finalList: [], // 搜尋時使用的 List

      meetHistoryNumber: null,

      sqlcommand: "",
      serverURL: "http://220.135.101.179/query?sql=",

      getProduct: [],
      data: {
        input: "",
        selection: null,
      },
    };
  },
  methods: {
    refreshLocalStorageList(){
      this.getLocaleStorage();
      
      let temp = this.localStorageList.reverse().concat(this.list);
      this.finalList = temp.filter((value, pos) => temp.indexOf(value) === pos);
    },

    selectItem(item) {
      this.data.selection = item;
    },

    onInput(event) {
      this.data.selection = null;
      this.data.input = event.input;
      this.filtered = event.items;
    },

    onBlur(event) {
      this.data.input = event.input;
      this.filtered = event.items;
    },

    getLocaleStorage() {
      let storage = JSON.parse(localStorage.getItem(this.LOCAL_STORAGE_KEY));
      if (storage != null) {
        this.localStorageList = storage;
      }
    },

    searchDataInDatabase() {
      if (this.filtered != null) {
        this.saveToLocalStorage();

        this.sqlcommand = "SELECT id FROM products WHERE name LIKE '%25" + this.data.input + "%25'";

        let fetchURL = (this.serverURL + this.sqlcommand).replace(/ /g,"%20");
        fetchURL = fetchURL.replace(/'/g, "%27");
        
         this.$http.get(fetchURL).then((response) => {    //Success
         this.getProduct = response.data;
         console.log(this.getProduct);
       },(response)=>{    //fail
         console.log(response);
       });
      }
      this.refreshLocalStorageList();
    },

    saveToLocalStorage() {
      this.localStorageList.push(this.data.input);
      localStorage.setItem(
        this.LOCAL_STORAGE_KEY,
        JSON.stringify(this.localStorageList)
      );
    },

    clearLocalStorage() {
      localStorage.setItem(this.LOCAL_STORAGE_KEY, JSON.stringify([]));
    },

    giveProductID(){
      this.$emit("productID", this.getProduct);
    }
  },
  computed: {}
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss">

#searchbar {
  margin: 0 auto;
  padding: 15px 20px;
  display: flex;
  justify-items: center;
  align-items: center;
  width: 70%;
  img {
    cursor: pointer;
    z-index: 100;
    position: relative;
    left: -2.3rem;
    padding: 0rem;
    @media (max-width: 300px) {
      display: none;
    }
  }
}

.simple-typeahead > input {
  width: 99%;
  min-height: 1.5rem;
  border-radius: 10px;
  border: none;
  border: solid 3px #ccc;
  /* margin-bottom: 0; */
}

.simple-typeahead .simple-typeahead-list .simple-typeahead-list-item {
  border-radius: 5px;
}
</style>
