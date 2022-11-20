<template>
  <div>
    <div
      id="factura"
      ref="facturaRef"
      v-if="factura"
      style="background-color: white; color: black; padding: 15px"
    >
      <p>Sentidos Tea House & Restaurant</p>
      <p>C.U.I.T. Nro 30692138747</p>
      <p>Ing. Brutos: 30692138747</p>
      <p>Inicio de Actividades: 17/12/1996</p>
      <p>IVA Responsable inscripto</p>
      <p>A CONSUMIDOR FINAL</p>
      <p>Cod. Factura : {{ factura.id }}</p>

      <p>Fecha: {{ factura.createdAt }}</p>

      <div>
        <div v-for="pedido in factura.pedidos" :key="pedido.id">
          <p>
            {{ pedido.producto.name }} x({{ pedido.cantidad }})
            <span> ${{ pedido.cantidad * pedido.producto.price }} </span>
          </p>
        </div>
        <h2>total ${{ factura.total }}</h2>
      </div>
      <img style="margin: 0 auto; width: 180px" src="../assets/qr.jpg" />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, watch } from "vue";
import { useRoute } from "vue-router";
import axios from "axios";
import jsPDF from "jspdf";
export default defineComponent({
  setup() {
    const route = useRoute();
    console.log(route.params);
    const { id } = route.params;
    const facturaRef = ref();
    const factura = ref();

    const fetchData = async () => {
      const { data } = await axios.get(
        `https://payloadback-production.up.railway.app/api/factura?where[id][equals]=${id}`
      );
      console.log(data);
      factura.value = data.docs[0];
      console.log(factura.value);
    };

    watch(facturaRef, (newValue) => {
      if (newValue) {
        console.log(facturaRef.value);
        const doc = new jsPDF();
        doc.html(facturaRef.value, {
          callback: (pdf) => {
            pdf.save("factura");
          },
          margin: [10, 10, 10, 10],
          autoPaging: "text",
          x: 0,
          y: 0,
          width: 200,
          windowWidth: 350,
        });
      }
    });

    fetchData();
    return {
      factura,
      facturaRef,
    };
  },
});
</script>

<style></style>
