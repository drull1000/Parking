<template>
  <div id="Main">
    <div>
      <div class="LabelDiv">
        <div class="PriceLabel">
          <span>Preço/hora: R$</span>
          <input type="number" min="0" v-model.number="price" />
        </div>
        <div class="SearchLabel">
          <img class="SearchIcon" src="../assets/search.svg" alt="Search" />
          <input v-model="searchKey" />
        </div>
      </div>
    </div>
    <div class="CreateCar">
      <label>Placa:</label>
      <input
        v-model="CarNumber"
        :class="{ GreyInput: CarNumber.length != 7 }"
        class="NameInput"
        @keyup.enter="AddCar"
        type="text"
        maxlength="7"
      />
      <button @click="AddCar" class="CreateButton">Adicionar</button>
    </div>
    <div class="TableValues">
      <span class="CarSpan">Placa</span>
      <span class="CarSpan">Entrada</span>
      <span class="CarSpan">Preço</span>
    </div>
    <div class="ParkingList">
      <li class="CarList" v-for="car in filteredItems" v-bind:key="car.id">
        <div class="CarWrapper">
          <p>{{ car.title | capitalize }}</p>
        </div>

        <div>
          <p>{{ car.hour }}:{{ car.minutes }}</p>
        </div>

        <div>
          <p>
            R${{ Math.floor((price * (updatedTime - car.arriveTime)) / 60) }}
          </p>
        </div>
        <button
          @click="RemoveCar(car)"
          class="DeleteButton"
          type="button"
          name="button"
          id="RemoveButton"
        >
          Deletar
        </button>
      </li>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      name: "Carlist",
      CarNumber: "",
      cars: [],
      price: 1,
      searchKey: "",
      updatedTime: 0,
    };
  },
  created() {
    setInterval(this.getPrice, 5000);
  },
  computed: {
    filteredItems() {
      const search = this.searchKey.toLowerCase().trim();
      if (!search) return this.cars;
      return this.cars.filter(
        (c) => c.title.toLowerCase().indexOf(search) > -1
      );
    },
  },
  watch: {
    cars: {
      handler() {
        console.log("cars array changed!");
        localStorage.setItem("cars", JSON.stringify(this.cars));
      },
      deep: true,
    },
  },
  mounted() {
    console.log("App Mounted");
    if (localStorage.getItem("cars"))
      this.cars = JSON.parse(localStorage.getItem("cars"));
  },
  methods: {
    getPrice() {
      const today = new Date();
      const time = today.getHours() * 60 + today.getMinutes();
      this.updatedTime = time;
    },
    AddCar() {
      const currentDate = new Date();
      const hour = currentDate.getHours();
      const minutes = currentDate.getMinutes();
      const carminutes = minutes < 10 ? "0" + minutes : minutes;
      const arriveTime = hour * 60 + minutes;
      const carId = Math.floor(Math.random() * 1000);
      if (this.CarNumber && this.CarNumber.length == 7) {
        this.cars.push({
          id: carId,
          hour: hour,
          minutes: carminutes,
          title: this.CarNumber,
          arriveTime: arriveTime,
        });
        this.CarNumber = "";
      }
    },
    RemoveCar(car) {
      const CarIndex = this.cars.indexOf(car);
      this.cars.splice(CarIndex, 1);
    },
  },
  filters: {
    capitalize(value) {
      return (
        value.substring(0, value.length - 4) +
        "-" +
        value.slice(3)
      ).toUpperCase();
    },
  },
};
</script>

<style>
@import "../styles/Carlist.css";
</styles>