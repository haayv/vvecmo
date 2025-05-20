<template>
  <header>
   <h1>VV-Recirculation-Tool</h1>
  </header>
  <main>
    <div>
      <form>
        <div>
          <label for="ef">ECMOFlow l/min</label>
          <input type="number" id="ef" v-model="ECMOFlow"/>
          <label for="satA">Sat Art O2%</label>
          <input type="number" id="satA" v-model="SatA" />
        </div>
        <div>
          <label for="satR">Sat Ret O2%</label>
          <input type="number" id="satR" v-model="SatR" />
          <label for="satCV">Sat Ctl Ven O2%</label>
          <input type="number" id="satCV" v-model="SatCV" />
        </div>
        <div>
          <label for="satD">Sat Drain O2%</label>
          <input type="number" id="satD" v-model="SatD" />
          <label for="Hb">Hb dl/l</label>
          <input type="number" id="Hb" v-model="Hb" />
        </div>
        <div>
          <label for="BSA">BSA m²</label>
          <input type="number" id="BSA" v-model="BSA"/>
        </div>
      </form>
      <p>Recirculation Factor: <span :class="RFStyle">{{RecirculationFactor.toFixed(2)}}</span></p>
      <p>Cardiac Output l/min: <span  v-if="HideOtherValues">{{CardiacOutput.toFixed(2)}}</span></p>
      <p>Oxygen Delivery dl/min: <span v-if="HideOtherValues">{{OxygenDelivery.toFixed(2)}}</span></p>
      <p>Oxygen Consumption dl/min: <span v-if="HideOtherValues">{{OxygenConsumption.toFixed(2)}}</span></p>
      <p>Real OxygenDelivery dl/min/m²: <span v-if="HideOtherValues">{{OxygenDeliveryReal.toFixed(2)}}</span></p>
      <p>Carring Capacity Of Arterial Blood ml/min: <span v-if="HideOtherValues">{{CarringCapacityOfArterialBlood.toFixed(2)}}</span></p>
      <p>Carring Capacity Of Venous Blood ml/min: <span v-if="HideOtherValues">{{CarringCapacityOfVenousBlood.toFixed(2)}}</span></p>
    </div>
  </main>
</template>

<script setup>
import { ref, computed } from 'vue';

const RECIRCULATION_FACTOR_THRESHOLD_1 = 1.15;
const RECIRCULATION_FACTOR_THRESHOLD_2 = 1.3;
const ECMOFlow = ref(0);
const SatA = ref(0);
const SatR = ref(0);
const SatCV = ref(0);
const SatD = ref(0);
const Hb = ref(0);
const BSA = ref(0);

const RFStyle = computed(() => {
  if (RecirculationFactor.value > RECIRCULATION_FACTOR_THRESHOLD_2) {
    return 'red';
  } else if (RecirculationFactor.value > RECIRCULATION_FACTOR_THRESHOLD_1) {
    return 'orange';
  } else {
    return 'green';
  }
})

const HideOtherValues = computed(() => {
  console.log(RecirculationFactor.value < RECIRCULATION_FACTOR_THRESHOLD_1);
  return RecirculationFactor.value < RECIRCULATION_FACTOR_THRESHOLD_1;
});

const RecirculationFactor = computed(() => {
  return (100 - SatCV.value) / (100 - SatD.value);
});

const CardiacOutput = computed(() => {
  return ECMOFlow.value * (SatR.value - SatD.value) / (SatA.value - SatCV.value);
});

const OxygenDelivery = computed(() => {
  return CardiacOutput.value * (CarringCapacityOfArterialBlood.value / 100);
});

const OxygenConsumption = computed(() => {
  return CardiacOutput.value * ((CarringCapacityOfArterialBlood.value - CarringCapacityOfVenousBlood.value) / 100);
});

const OxygenDeliveryReal = computed(() => {
  return (OxygenDelivery.value / BSA.value) / RecirculationFactor.value;
});

const CarringCapacityOfArterialBlood = computed(() => {
  return Hb.value * 1.36 * SatA.value;
});
const CarringCapacityOfVenousBlood = computed(() => {
  return Hb.value * 1.36 * SatCV.value;
});

</script>

<style scoped>
.red {
  color: red;
}
.green {
  color: green;
}
.orange {
  color: orange;
}
</style>
