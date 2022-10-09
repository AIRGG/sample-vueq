<script setup lang="ts">
import { useQuasar } from 'quasar';
import { useMyGlobalState } from 'src/stores/myglobalstate';
import { computed, reactive, Ref, ref, watch } from 'vue';
import MyComponent from 'src/components/MyComponent.vue';

const $q = useQuasar();
const myGlobalState = useMyGlobalState();

const listData: any = ref([]);

const addRow = () => {
  const tmpJsn = {
    no: listData.value.length + 1,
    nama: '',
  };
  listData.value.push(tmpJsn);
};

// function ketika dapet callback dari child
const addRowChild = (data: any) => {
  const tmpJsn = {
    no: listData.value.length + 1,
    nama: data,
  };
  listData.value.push(tmpJsn);
};

const removeRow = (index: number) => {
  listData.value.splice(index, 1);
};
const pesanKonfirmasi = () => {
  $q.dialog({
    title: 'Confirm',
    message: 'Yakin ingin melakukan sesuatu?',
    cancel: true,
    persistent: true,
  })
    .onOk(() => {
      // console.log('>>>> OK')
    })
    .onCancel(() => {
      // console.log('>>>> Cancel')
    })
    .onDismiss(() => {
      // console.log('I am triggered on both OK and Cancel')
    });
};

// -- handling form

// bisa satuan kaya gini by variable
const txtBuah = ref(''); // ini valuenya untuk inputan
const txtHewan = ref(''); // ini valuenya untuk inputan

// atau grouping by json kaya gini
const jsnBenda = reactive({
  bendaMati: 'gelas',
  bendaHidup: 'orang',
});

// ini behaviour
const disableInp1 = ref(false);
const hiddenInp2 = ref(true);

const toogleForm = () => {
  disableInp1.value = !disableInp1.value;
  hiddenInp2.value = !hiddenInp2.value;
};

// q-select
// const officeSelect = ref('');
// const officeData = ref('');
// const warehouseSelect = ref('');
// const warehouseData = ref('');

const location = reactive({
  officeSelect: '',
  officeData: ['A', 'B', 'C'],
  warehouseSelect: '',
  warehouseData: [] as string[],
});

const wareData = reactive({
  A: ['AA1', 'AA2', 'AA3'],
  B: ['BB1', 'BB2', 'BB3'],
  C: ['CC1', 'CC2', 'CC3'],
});

watch(
  () => location.officeSelect,
  (newVal, oldVal) => {
    location.warehouseData = wareData[newVal as keyof typeof wareData];
  }
);
// const listData = computed(() => location.officeSelect);
</script>
<template>
  <q-page padding>
    {{ location }}
    <div class="row">
      <div class="col-6">
        office
        <q-select
          v-model="location.officeSelect"
          :options="location.officeData"
          label="Standard"
        />
      </div>
      <div class="col-6">
        warehouse
        <q-select
          v-model="location.warehouseSelect"
          :options="location.warehouseData"
          label="Standard"
        />
      </div>
    </div>
    <!-- button dengan global state, bisa diakses dimana aja -->
    <q-btn @click="myGlobalState.fnTambahinAngka" color="negative">
      Angka Asli: {{ myGlobalState.myangka }} klik untuk nambah angka dikali:
      {{ myGlobalState.propAngkaDikali2 }} | diakar:
      {{ myGlobalState.propAngkaAkar2 }}
    </q-btn>
    <q-input
      v-model="myGlobalState.myangka"
      label="ubah global pake inputan"
      type="number"
      dense
      outlined
    />
    <hr />
    <!-- cara trigger komponen lain -->
    <div class="row">
      <div class="col-12">
        <q-btn @click="toogleForm">Toggle Form dibawah</q-btn>
      </div>
    </div>
    <div class="row">
      <div class="col-6">
        <!-- cara pakai inputan -->
        <q-input
          dense
          outlined
          label="buah"
          v-model="txtBuah"
          :disable="disableInp1"
        />
        {{ txtBuah }}
      </div>
      <div class="col-6">
        <q-input
          dense
          outlined
          label="hewan"
          v-model="txtHewan"
          v-if="hiddenInp2"
        />
        {{ txtHewan }}
      </div>
    </div>
    <div class="row">
      <div class="col-6">
        <!-- cara pakai inputan -->
        <q-input
          dense
          outlined
          label="benda mati"
          v-model="jsnBenda.bendaMati"
          :disable="disableInp1"
        />
      </div>
      <div class="col-6">
        <q-input
          dense
          outlined
          label="benda hidup"
          v-model="jsnBenda.bendaHidup"
          v-if="hiddenInp2"
        />
      </div>
      {{ jsnBenda }}
    </div>
    <hr />
    <!-- custom input data didalam array -->
    <q-btn @click="addRow" color="blue">Add Row</q-btn>
    <table>
      <tr>
        <td></td>
        <td>No</td>
        <td>Nama</td>
      </tr>
      <tr v-for="(x, k) in listData" :key="x">
        <td><q-btn color="red" @click="removeRow(k)">x</q-btn></td>
        <td>{{ x.no }}</td>
        <td>
          <q-input dense outlined v-model="x.nama" label="Input nama anda" />
        </td>
      </tr>
    </table>
    {{ listData }}
    <q-btn @click="pesanKonfirmasi">Pesan Konfirmasi</q-btn>
    <hr />
    <MyComponent msg="Comp1"></MyComponent>
    <!-- kalau boolean cukup declare aja propertynya gk usah dikasih value -->
    <MyComponent
      msg="Comp2"
      show-btn
      @addRowFromChild="addRowChild"
    ></MyComponent>
  </q-page>
</template>

<style scoped></style>
