<template>
  <header>
   <h1>VV-Recirculation-Tool</h1>
  </header>
  <main>
    <div>
      <form>
        <div>
          <label for="ef">ECMOFlow</label>
          <input type="number" id="ef" v-model="ECMOFlow"/>
          <label for="satA">SatA%O2</label>
          <input type="number" id="satA" v-model="SatA" />
        </div>
        <div>
          <label for="satR">SatRet%O2</label>
          <input type="number" id="satR" v-model="SatR" />
          <label for="satCV">Sat Ctl Ven</label>
          <input type="number" id="satCV" v-model="SatCV" />
        </div>
        <div>
          <label for="satD">SatDrain%O2</label>
          <input type="number" id="satD" v-model="SatD" />
          <label for="Hb">Hb</label>
          <input type="number" id="Hb" v-model="Hb" />
        </div>
        <div>
          <label for="BSA">BSA</label>
          <input type="number" id="BSA" v-model="BSA"/>
        </div>
      </form>
      <p>RecirculationFactor: <span :class="RFStyle">{{RecirculationFactor}}</span></p>
      <p>CardiacOutput: {{CardiacOutput}}</p>
      <p>OxygenDelivery: {{OxygenDelivery}}</p>
      <p>OxygenConsumption: {{OxygenConsumption}}</p>
      <p>OxygenDeliveryIndex: {{OxygenDeliveryIndex}}</p>
      <p>CarringCapacityOfArterialBlood: {{CarringCapacityOfArterialBlood}}</p>
      <p>CarringCapacityOfVenousBlood: {{CarringCapacityOfVenousBlood}}</p>
    </div>
  </main>
</template>

<script setup>
import { ref, watch, computed } from 'vue';

const ECMOFlow = ref(0);
const SatA = ref(0);
const SatR = ref(0);
const SatCV = ref(0);
const SatD = ref(0);
const Hb = ref(0);
const BSA = ref(0);

const RFStyle = computed(() => {
  if (RecirculationFactor.value > 1.3) {
    return 'red';
  } else if (RecirculationFactor.value > 1.15) {
    return 'orange';
  } else {
    return 'green';
  }
})

const RecirculationFactor = computed(() => {
  return (100 - SatCV.value) / (100 - SatD.value);
});

const CardiacOutput = computed(() => {
  return ECMOFlow.value * (SatR.value - SatD.value) / (SatA.value - SatCV.value);
});

const OxygenDelivery = computed(() => {
  return SatA.value * (CarringCapacityOfArterialBlood.value / 100);
});

const OxygenConsumption = computed(() => {
  return SatA.value * (CarringCapacityOfArterialBlood.value - CarringCapacityOfVenousBlood.value) / 100;
});

const OxygenDeliveryIndex = computed(() => {
  return (OxygenDelivery.value / BSA.value) / RecirculationFactor.value;
});

const CarringCapacityOfArterialBlood = computed(() => {
  return Hb.value * 1.36 * SatA.value;
});
const CarringCapacityOfVenousBlood = computed(() => {
  return Hb.value * 1.36 * SatCV.value;
});

/*func RecirculationFactor(SatCV, SatD) {
  return (100-SatCV)/(100-SatD);
}

func CardiacOutput(ECMOFlow, SatA, SatR, SatCV, SatD) {
  return ECMOFlow * (SatR - SatD) / (SatA - SatCV);
}

func OxygenDelivery(SatA, Hb) {
  return SatA * (CarringCapacityOfArterialBlood(SatA, Hb)/100);
}

func OxygenConsumption(SatA, SatCV, Hb) {
  return SatA * (CarringCapacityOfArterialBlood(SatA, Hb)-CarringCapacityOfVenousBlood(SatCV, Hb))/100;
}

func OxygenDeliveryIndex(SatA,SatCV, SatD, Hb, BSA) {
  return (OxygenDelivery(SatA, Hb)/BSA)/RecirculationFactor(SatCV, SatD);
}

func CarringCapacityOfArterialBlood(SatA, Hb) {
  return Hb * 1.36 * SatA;
}

func CarringCapacityOfVenousBlood(SatCV, Hb) {
  return Hb * 1.36 * SatCV;
}*/

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
