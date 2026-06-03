<template>
  <div class="space-y-8">
    <div class="flex items-center justify-between">
      <div>
        <h1 class="text-2xl font-bold text-gray-900">Hasil TOPSIS</h1>
        <p class="text-gray-400 text-sm mt-1">
          Ranking provider berdasarkan metode TOPSIS
        </p>
      </div>
      <button
        @click="hitung"
        :disabled="loading"
        class="flex items-center gap-2 bg-gray-900 text-white px-4 py-2.5 rounded-xl text-sm font-medium hover:bg-gray-700 transition-all disabled:opacity-50"
      >
        {{ loading ? "Menghitung..." : "🧮 Hitung TOPSIS" }}
      </button>
    </div>

    <div
      v-if="loading"
      class="flex flex-col items-center justify-center py-20 gap-3"
    >
      <div
        class="w-8 h-8 border-2 border-gray-900 border-t-transparent rounded-full animate-spin"
      />
      <p class="text-sm text-gray-400">Sedang menghitung...</p>
    </div>

    <template v-else-if="hasil.length > 0">
      <!-- Top 3 -->
      <div class="grid grid-cols-3 gap-5">
        <div
          v-for="(item, i) in hasil.slice(0, 3)"
          :key="item.provider_id"
          class="rounded-2xl p-6 text-center space-y-3 border"
          :class="[
            i === 0
              ? 'bg-gray-900 text-white border-gray-900'
              : i === 1
                ? 'bg-white border-gray-200'
                : 'bg-white border-gray-100',
          ]"
        >
          <div class="text-3xl">
            {{ i === 0 ? "🥇" : i === 1 ? "🥈" : "🥉" }}
          </div>
          <div>
            <p class="font-bold text-lg">{{ item.nama }}</p>
            <p
              class="text-sm mt-1"
              :class="i === 0 ? 'text-gray-400' : 'text-gray-400'"
            >
              Skor: {{ item.skor.toFixed(4) }}
            </p>
          </div>
          <div
            class="inline-block text-xs font-medium px-3 py-1 rounded-full"
            :class="
              i === 0 ? 'bg-white/20 text-white' : 'bg-gray-100 text-gray-600'
            "
          >
            Ranking #{{ i + 1 }}
          </div>
        </div>
      </div>

      <!-- Tabel Lengkap -->
      <div class="bg-white rounded-2xl border border-gray-100 overflow-hidden">
        <div class="px-6 py-4 border-b border-gray-100">
          <p class="text-sm font-medium text-gray-700">Tabel Ranking Lengkap</p>
        </div>
        <table class="w-full">
          <thead class="bg-gray-50 border-b border-gray-100">
            <tr>
              <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
                Ranking
              </th>
              <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
                Provider
              </th>
              <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
                Skor Preferensi
              </th>
              <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
                D+ (Ideal Positif)
              </th>
              <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
                D- (Ideal Negatif)
              </th>
              <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
                Bar
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-50">
            <tr
              v-for="(item, i) in hasil"
              :key="item.provider_id"
              class="hover:bg-gray-50 transition-colors"
            >
              <td class="px-6 py-4">
                <div
                  class="w-7 h-7 rounded-full flex items-center justify-center text-xs font-bold"
                  :class="
                    i === 0
                      ? 'bg-gray-900 text-white'
                      : 'bg-gray-100 text-gray-600'
                  "
                >
                  {{ i + 1 }}
                </div>
              </td>
              <td class="px-6 py-4 text-sm font-medium text-gray-900">
                {{ item.nama }}
              </td>
              <td class="px-6 py-4 text-sm font-mono text-gray-700">
                {{ item.skor.toFixed(6) }}
              </td>
              <td class="px-6 py-4 text-sm font-mono text-gray-500">
                {{ item.dPlus.toFixed(6) }}
              </td>
              <td class="px-6 py-4 text-sm font-mono text-gray-500">
                {{ item.dMinus.toFixed(6) }}
              </td>
              <td class="px-6 py-4">
                <div class="w-32 bg-gray-100 rounded-full h-2">
                  <div
                    class="bg-gray-900 h-2 rounded-full transition-all"
                    :style="{ width: item.skor * 100 + '%' }"
                  />
                </div>
              </td>
            </tr>
          </tbody>
        </table>
      </div>

      <!-- Langkah TOPSIS -->
      <div class="bg-white rounded-2xl border border-gray-100 overflow-hidden">
        <div class="px-6 py-4 border-b border-gray-100">
          <p class="text-sm font-medium text-gray-700">
            Langkah-langkah Perhitungan TOPSIS
          </p>
        </div>
        <div class="p-6 space-y-6">
          <div class="space-y-3">
            <p class="text-sm font-semibold text-gray-900">
              1. Matriks Keputusan Ternormalisasi
            </p>
            <div class="overflow-x-auto">
              <table class="text-xs w-full">
                <thead>
                  <tr class="bg-gray-50">
                    <th class="text-left px-4 py-2 text-gray-400 font-medium">
                      Provider
                    </th>
                    <th
                      v-for="k in steps.kriteria"
                      :key="k.id"
                      class="px-4 py-2 text-gray-400 font-medium"
                    >
                      {{ k.nama }}
                    </th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-50">
                  <tr v-for="row in steps.normalisasi" :key="row.nama">
                    <td class="px-4 py-2 font-medium text-gray-700">
                      {{ row.nama }}
                    </td>
                    <td
                      v-for="(v, vi) in row.values"
                      :key="vi"
                      class="px-4 py-2 text-center font-mono text-gray-500"
                    >
                      {{ v.toFixed(4) }}
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div class="space-y-3">
            <p class="text-sm font-semibold text-gray-900">
              2. Matriks Terbobot
            </p>
            <div class="overflow-x-auto">
              <table class="text-xs w-full">
                <thead>
                  <tr class="bg-gray-50">
                    <th class="text-left px-4 py-2 text-gray-400 font-medium">
                      Provider
                    </th>
                    <th
                      v-for="k in steps.kriteria"
                      :key="k.id"
                      class="px-4 py-2 text-gray-400 font-medium"
                    >
                      {{ k.nama }}
                    </th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-50">
                  <tr v-for="row in steps.terbobot" :key="row.nama">
                    <td class="px-4 py-2 font-medium text-gray-700">
                      {{ row.nama }}
                    </td>
                    <td
                      v-for="(v, vi) in row.values"
                      :key="vi"
                      class="px-4 py-2 text-center font-mono text-gray-500"
                    >
                      {{ v.toFixed(4) }}
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>

          <div class="space-y-3">
            <p class="text-sm font-semibold text-gray-900">
              3. Solusi Ideal Positif (A+) & Negatif (A-)
            </p>
            <div class="overflow-x-auto">
              <table class="text-xs w-full">
                <thead>
                  <tr class="bg-gray-50">
                    <th class="text-left px-4 py-2 text-gray-400 font-medium">
                      Solusi
                    </th>
                    <th
                      v-for="k in steps.kriteria"
                      :key="k.id"
                      class="px-4 py-2 text-gray-400 font-medium"
                    >
                      {{ k.nama }}
                    </th>
                  </tr>
                </thead>
                <tbody class="divide-y divide-gray-50">
                  <tr>
                    <td class="px-4 py-2 font-medium text-green-700">
                      A+ (Ideal Positif)
                    </td>
                    <td
                      v-for="(v, vi) in steps.idealPositif"
                      :key="vi"
                      class="px-4 py-2 text-center font-mono text-green-600"
                    >
                      {{ v.toFixed(4) }}
                    </td>
                  </tr>
                  <tr>
                    <td class="px-4 py-2 font-medium text-red-700">
                      A- (Ideal Negatif)
                    </td>
                    <td
                      v-for="(v, vi) in steps.idealNegatif"
                      :key="vi"
                      class="px-4 py-2 text-center font-mono text-red-600"
                    >
                      {{ v.toFixed(4) }}
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </template>

    <div
      v-else
      class="flex flex-col items-center justify-center py-20 bg-white rounded-2xl border border-gray-100 text-center"
    >
      <span class="text-4xl mb-3">🧮</span>
      <p class="text-gray-500 font-medium">Belum ada hasil perhitungan</p>
      <p class="text-gray-400 text-sm mt-1">
        Klik "Hitung TOPSIS" untuk memulai perhitungan
      </p>
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({ layout: "default" });

const { $supabase } = useNuxtApp();

interface Kriteria {
  id: string;
  nama: string;
  bobot: number;
  jenis: string;
}

interface Provider {
  id: string;
  nama: string;
}

interface NilaiProvider {
  provider_id: string;
  kriteria_id: string;
  nilai: number;
}

interface HasilItem {
  provider_id: string;
  nama: string;
  skor: number;
  dPlus: number;
  dMinus: number;
}

interface StepsData {
  kriteria: Kriteria[];
  normalisasi: { nama: string; values: number[] }[];
  terbobot: { nama: string; values: number[] }[];
  idealPositif: number[];
  idealNegatif: number[];
}

const loading = ref(false);
const hasil = ref<HasilItem[]>([]);
const steps = ref<StepsData>({
  kriteria: [],
  normalisasi: [],
  terbobot: [],
  idealPositif: [],
  idealNegatif: [],
});

async function hitung() {
  loading.value = true;

  const { data: kriteriaRaw } = await $supabase
    .from("kriteria")
    .select("*")
    .order("created_at");

  const { data: providerRaw } = await $supabase
    .from("provider")
    .select("*")
    .order("created_at");

  const { data: nilaiRaw } = await $supabase.from("nilai_provider").select("*");

  const kriteria = (kriteriaRaw as Kriteria[]) ?? [];
  const provider = (providerRaw as Provider[]) ?? [];
  const nilaiData = (nilaiRaw as NilaiProvider[]) ?? [];

  if (kriteria.length === 0 || provider.length === 0) {
    loading.value = false;
    return;
  }

  // Bangun matriks keputusan
  const matrix: number[][] = provider.map((p) =>
    kriteria.map((k) => {
      const found = nilaiData.find(
        (n) => n.provider_id === p.id && n.kriteria_id === k.id,
      );
      return found ? Number(found.nilai) : 0;
    }),
  );

  // 1. Normalisasi (pembagi = akar jumlah kuadrat tiap kolom)
  const norm: number[][] = matrix.map((row) => [...row]);
  for (let j = 0; j < kriteria.length; j++) {
    const sumSq = Math.sqrt(
      matrix.reduce((sum, row) => sum + (row[j] ?? 0) ** 2, 0),
    );
    for (let i = 0; i < provider.length; i++) {
      const normRow = norm[i];
      if (normRow !== undefined) {
        normRow[j] = sumSq === 0 ? 0 : (matrix[i]?.[j] ?? 0) / sumSq;
      }
    }
  }

  // 2. Matriks terbobot
  const bobot = kriteria.map((k) => k.bobot);
  const terbobot: number[][] = norm.map((row) =>
    row.map((v, j) => v * (bobot[j] ?? 0)),
  );

  // 3. Solusi ideal positif & negatif
  const idealPos = kriteria.map((k, j) => {
    const kolom = terbobot.map((r) => r[j] ?? 0);
    return k.jenis === "benefit" ? Math.max(...kolom) : Math.min(...kolom);
  });

  const idealNeg = kriteria.map((k, j) => {
    const kolom = terbobot.map((r) => r[j] ?? 0);
    return k.jenis === "benefit" ? Math.min(...kolom) : Math.max(...kolom);
  });

  // 4. Jarak D+ dan D-
  const dPlus = terbobot.map((row) =>
    Math.sqrt(
      row.reduce((sum, v, j) => sum + (v - (idealPos[j] ?? 0)) ** 2, 0),
    ),
  );

  const dMinus = terbobot.map((row) =>
    Math.sqrt(
      row.reduce((sum, v, j) => sum + (v - (idealNeg[j] ?? 0)) ** 2, 0),
    ),
  );

  // 5. Skor preferensi
  const skorList: HasilItem[] = provider.map((p, i) => {
    const dp = dPlus[i] ?? 0;
    const dm = dMinus[i] ?? 0;
    return {
      provider_id: p.id,
      nama: p.nama,
      skor: dp + dm === 0 ? 0 : dm / (dp + dm),
      dPlus: dp,
      dMinus: dm,
    };
  });

  hasil.value = skorList.sort((a, b) => b.skor - a.skor);

  // Simpan ke DB (hapus dulu yang lama)
  await $supabase
    .from("hasil_topsis")
    .delete()
    .neq("id", "00000000-0000-0000-0000-000000000000");

  await $supabase.from("hasil_topsis").insert(
    hasil.value.map((h, i) => ({
      provider_id: h.provider_id,
      skor_preferensi: h.skor,
      ranking: i + 1,
    })),
  );

  steps.value = {
    kriteria,
    normalisasi: provider.map((p, i) => ({
      nama: p.nama,
      values: norm[i] ?? [],
    })),
    terbobot: provider.map((p, i) => ({
      nama: p.nama,
      values: terbobot[i] ?? [],
    })),
    idealPositif: idealPos,
    idealNegatif: idealNeg,
  };

  loading.value = false;
}

onMounted(hitung);
</script>
