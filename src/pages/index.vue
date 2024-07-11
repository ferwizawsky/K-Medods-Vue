<script setup>
import { ref } from "vue";
import * as XLSX from "xlsx";
import {
  Table,
  TableBody,
  TableCaption,
  TableCell,
  TableHead,
  TableHeader,
  TableRow,
} from "@/components/ui/table";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Button } from "@/components/ui/button";

const dataLoaded = ref(false);
const points = ref([]);
//"SP-36", "NPK", "ZA", "Petroganik", "Urea"
const selectedType = ref("SP-36");
const selectedMenu = ref(0);
const listType = [
  {
    name: "SP-36",
    c1: [324, 354, 0],
    c2: [178, 230, 0],
    c3: [315, 341, 14],
    color: "#FFD0D0",
  },
  {
    name: "NPK",
    c1: [877, 1527, 6],
    c2: [2030, 1674, 16],
    c3: [1327, 1940, 0],
    color: "#F9F9E0",
  },
  {
    name: "Petroganik",
    c1: [261, 920, 38],
    c2: [423, 1380, 0],
    c3: [413, 1215, 1],
    color: "#D1D8C5",
  },
  {
    name: "Urea",
    c1: [228, 469, 29],
    c2: [249, 545, 0],
    c3: [1435, 2703.7, 7.3],
    color: "#E49BFF",
  },
  {
    name: "ZA",
    c1: [337, 707, 22],
    c2: [665, 1165, 0],
    c3: [521, 1016.6, 0.4],
    color: "#CDE8E5",
  },
];
const listByType = ref([]);

const handleFileChange = (event) => {
  const file = event.target.files[0];
  const reader = new FileReader();
  reader.onload = (e) => {
    const data = new Uint8Array(e.target.result);
    const workbook = XLSX.read(data, { type: "array" });
    const sheetName = workbook.SheetNames[0];
    const worksheet = workbook.Sheets[sheetName];
    const parsedData = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
    const pointsData = parsedData.slice(1).map((row) => ({
      tahun: row[1],
      kecamatan: row[2],
      nama: row[3],
      x: row[4],
      y: row[5],
      z: row[6],
    }));
    for (let type of listType) {
      listByType.value.push({
        type,
        list: pointsData.filter((item) => item.nama == type.name),
      });
    }
    console.log(listByType.value);
    points.value = pointsData;
    dataLoaded.value = true;
  };
  reader.readAsArrayBuffer(file);
};

const c1 = (point) => {
  const tmp = listByType.value.find((e) => e?.type?.name == point.nama)?.type;
  const J = tmp?.c1?.[0];
  const K = tmp?.c1?.[1];
  const L = tmp?.c1?.[2];
  return Math.sqrt(
    Math.pow(point.x - J, 2) +
      Math.pow(point.y - K, 2) +
      Math.pow(point.z - L, 2)
  );
};

function c2(point) {
  const tmp = listByType.value.find((e) => e?.type?.name == point.nama)?.type;
  const J = tmp?.c2?.[0];
  const K = tmp?.c2?.[1];
  const L = tmp?.c2?.[2];
  var distance = Math.sqrt(
    Math.pow(point.x - J, 2) +
      Math.pow(point.y - K, 2) +
      Math.pow(point.z - L, 2)
  );
  return distance;
}

function c3(point) {
  const tmp = listByType.value.find((e) => e?.type?.name == point.nama)?.type;
  const J = tmp?.c3?.[0];
  const K = tmp?.c3?.[1];
  const L = tmp?.c3?.[2];
  var distance = Math.sqrt(
    Math.pow(point.x - J, 2) +
      Math.pow(point.y - K, 2) +
      Math.pow(point.z - L, 2)
  );
  return distance;
}

function getClass(point, item) {
  let css = "";
  if (
    point.x == item?.type?.c1?.[0] &&
    point.y == item?.type?.c1?.[1] &&
    point.z == item?.type?.c1?.[2]
  )
    css = "bg-lime-400";
  if (
    point.x == item?.type?.c2?.[0] &&
    point.y == item?.type?.c2?.[1] &&
    point.z == item?.type?.c2?.[2]
  )
    css = "bg-amber-400";
  if (
    point.x == item?.type?.c3?.[0] &&
    point.y == item?.type?.c3?.[1] &&
    point.z == item?.type?.c3?.[2]
  )
    css = "bg-rose-400";
  return css;
}

function calculateDistance(point, list) {
  let tmp = [];
  for (let other of list) {
    tmp.push(
      Math.sqrt(
        Math.pow(point.x - other.x, 2) +
          Math.pow(point.y - other.y, 2) +
          Math.pow(point.z - other.z, 2)
      )
    );
  }
  return tmp.sort((a, b) => a - b);
}

function calculateDistanceSum(E3, F3, G3, points) {
  let sum = 0;
  for (let i = 0; i < points.length; i++) {
    let E = points[i]?.x;
    let F = points[i]?.y;
    let G = points[i]?.z;
    sum += Math.sqrt(
      Math.pow(E3 - E, 2) + Math.pow(F3 - F, 2) + Math.pow(G3 - G, 2)
    );
  }
  return sum / (points.length - 1);
}

function kedekatan(O5, P5, Q5) {
  let tmp = Math.min(O5, P5, Q5);
  let stat = "Tinggi (Bagus)";
  let cluster = 1;
  if (tmp == O5) {
    stat = "Tinggi (Bagus)";
    cluster = 1;
  }
  if (tmp == P5) {
    stat = "Sedang";
    cluster = 2;
  }
  if (tmp == Q5) {
    stat = "Kurang";
    cluster = 3;
  }
  return {
    status: stat,
    tmp: tmp,
    cluster,
  };
}
</script>
<template>
  <div class="max-w-[1200px] p-4 pt-10 mx-auto">
    <div class="text-center font-semibold text-2xl max-w-lg mx-auto">
      PENGELOMPOKAN PUPUK BERSUBSIDI MENGGUNAKAN METODE K-MEDOIDS CLUSTERING
    </div>
    <div>
      <div class="py-10">
        <span class="mr-5">Contoh File Excel</span>
        <a href="/bahan_mentah_kmdoids.xlsx">
          <Button>Download Excel</Button></a
        >
      </div>
      <div class="mb-10">
        <Label> Masukkan File Excel </Label>
        <Input type="file" @change="handleFileChange" />
      </div>
    </div>
    <div v-if="dataLoaded">
      <div class="grid lg:grid-cols-2 gap-2 justify-between">
        <div class="space-x-4 mb-4">
          <Button
            v-for="item in listByType"
            @click="selectedType = item.type?.name"
            size="sm"
            :variant="item?.type?.name == selectedType ? '' : 'outline'"
          >
            {{ item?.type?.name }}
          </Button>
        </div>
        <div class="space-x-4">
          <Button
            @click="selectedMenu = 0"
            :variant="selectedMenu == 0 ? '' : 'outline'"
            size="sm"
            >K-Medoids</Button
          >
          <Button
            @click="selectedMenu = 1"
            :variant="selectedMenu == 1 ? '' : 'outline'"
            size="sm"
            >Shilouette</Button
          >
        </div>
      </div>
      <div class="flex space-x-8 mb-6">
        <div class="flex items-center space-x-2">
          <div class="w-4 h-4 bg-lime-400"></div>
          <div>Cluster 1 = C1</div>
        </div>

        <div class="flex items-center space-x-2">
          <div class="w-4 h-4 bg-amber-400"></div>
          <div>Cluster 2 = C2</div>
        </div>

        <div class="flex items-center space-x-2">
          <div class="w-4 h-4 bg-rose-400"></div>
          <div>Cluster 3 = C3</div>
        </div>
      </div>
      <div v-for="item in listByType">
        <Table v-if="selectedType == item?.type?.name">
          <TableHeader>
            <TableRow>
              <th>No.</th>
              <th>Tahun</th>
              <th>Kecamatan</th>
              <th>Nama</th>
              <th>Alokasi</th>
              <th>Realisasi</th>
              <th>Sisa/Kurang</th>
              <th v-if="selectedMenu == 0">C1</th>
              <th v-if="selectedMenu == 0">C2</th>
              <th v-if="selectedMenu == 0">C3</th>
              <th v-if="selectedMenu == 0">Kedekatan</th>
              <th v-if="selectedMenu == 1">a(i)</th>
              <th v-if="selectedMenu == 1">b(i)</th>
              <th v-if="selectedMenu == 1">Koefisien</th>
              <th>Hasil</th>
            </TableRow>
          </TableHeader>
          <TableBody>
            <TableRow
              :class="getClass(point, item)"
              v-for="(point, index) in item.list"
              :key="index"
            >
              <TableCell>{{ index + 1 }}</TableCell>
              <TableCell>{{ point.tahun }}</TableCell>
              <TableCell>{{ point.kecamatan }}</TableCell>
              <TableCell>{{ point.nama }}</TableCell>

              <TableCell>{{ point.x }}</TableCell>
              <TableCell>{{ point.y }}</TableCell>
              <TableCell>{{ point.z }}</TableCell>
              <TableCell v-if="selectedMenu == 0">{{ c1(point) }}</TableCell>
              <TableCell v-if="selectedMenu == 0">{{ c2(point) }}</TableCell>
              <TableCell v-if="selectedMenu == 0">{{ c3(point) }}</TableCell>
              <TableCell v-if="selectedMenu == 0">{{
                kedekatan(c1(point), c2(point), c3(point))?.tmp
              }}</TableCell>
              <TableCell v-if="selectedMenu == 1">
                {{
                  calculateDistanceSum(
                    point.x,
                    point.y,
                    point.z,
                    item.list.filter(
                      (e) =>
                        kedekatan(c1(e), c2(e), c3(e))?.cluster ==
                        kedekatan(c1(point), c2(point), c3(point))?.cluster
                    )
                  )
                }}
              </TableCell>
              <TableCell v-if="selectedMenu == 1">
                {{
                  calculateDistance(
                    point,
                    item.list.filter(
                      (e) =>
                        kedekatan(c1(e), c2(e), c3(e))?.cluster !=
                        kedekatan(c1(point), c2(point), c3(point))?.cluster
                    )
                  )?.[0]
                }}
              </TableCell>
              <TableCell v-if="selectedMenu == 1">
                {{
                  1 /
                  (calculateDistanceSum(
                    point.x,
                    point.y,
                    point.z,
                    item.list.filter(
                      (e) =>
                        kedekatan(c1(e), c2(e), c3(e))?.cluster ==
                        kedekatan(c1(point), c2(point), c3(point))?.cluster
                    )
                  ) -
                    calculateDistance(
                      point,
                      item.list.filter(
                        (e) =>
                          kedekatan(c1(e), c2(e), c3(e))?.cluster !=
                          kedekatan(c1(point), c2(point), c3(point))?.cluster
                      )
                    )?.[0])
                }}
              </TableCell>
              <TableCell>{{
                kedekatan(c1(point), c2(point), c3(point))?.status
              }}</TableCell>
            </TableRow>
          </TableBody>
        </Table>
      </div>
    </div>
  </div>
</template>
