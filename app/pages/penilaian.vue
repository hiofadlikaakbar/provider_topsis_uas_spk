<template>
  <div class="space-y-8">
    <div>
      <h1 class="text-2xl font-bold text-gray-900">Penilaian</h1>
      <p class="text-gray-400 text-sm mt-1">
        Isi nilai setiap provider untuk masing-masing kriteria
      </p>
    </div>

    <div
      v-if="
        !loading && (kriteriaList.length === 0 || providerList.length === 0)
      "
      class="bg-yellow-50 border border-yellow-100 rounded-2xl p-6 flex items-start gap-4"
    >
      <span class="text-2xl">⚠️</span>
      <div>
        <p class="font-medium text-yellow-800">Data belum lengkap</p>
        <p class="text-sm text-yellow-600 mt-1">
          Pastikan kamu sudah menambahkan
          <NuxtLink to="/kriteria" class="underline font-medium"
            >kriteria</NuxtLink
          >
          dan
          <NuxtLink to="/provider" class="underline font-medium"
            >provider</NuxtLink
          >
          terlebih dahulu.
        </p>
      </div>
    </div>

    <div
      v-else-if="!loading"
      class="bg-white rounded-2xl border border-gray-100 overflow-hidden"
    >
      <div
        class="px-6 py-4 border-b border-gray-100 flex items-center justify-between"
      >
        <p class="text-sm font-medium text-gray-700">Matriks Penilaian</p>
        <button
          @click="simpanSemua"
          :disabled="saving"
          class="flex items-center gap-2 bg-gray-900 text-white px-4 py-2 rounded-xl text-sm font-medium hover:bg-gray-700 transition-all disabled:opacity-50"
        >
          {{ saving ? "Menyimpan..." : "💾 Simpan Semua" }}
        </button>
      </div>

      <div class="overflow-x-auto">
        <table class="w-full">
          <thead class="bg-gray-50 border-b border-gray-100">
            <tr>
              <th
                class="text-left text-xs font-medium text-gray-400 px-6 py-4 min-w-40"
              >
                Provider
              </th>
              <th
                v-for="k in kriteriaList"
                :key="k.id"
                class="text-left text-xs font-medium text-gray-400 px-4 py-4 min-w-36"
              >
                <div>{{ k.nama }}</div>
                <div class="flex items-center gap-1.5 mt-1">
                  <span
                    class="px-1.5 py-0.5 rounded text-xs"
                    :class="
                      k.jenis === 'benefit'
                        ? 'bg-green-50 text-green-600'
                        : 'bg-red-50 text-red-600'
                    "
                  >
                    {{ k.jenis === "benefit" ? "↑" : "↓" }} {{ k.jenis }}
                  </span>
                  <span class="text-gray-300"
                    >{{ (k.bobot * 100).toFixed(0) }}%</span
                  >
                </div>
              </th>
            </tr>
          </thead>
          <tbody class="divide-y divide-gray-50">
            <tr
              v-for="p in providerList"
              :key="p.id"
              class="hover:bg-gray-50 transition-colors"
            >
              <td class="px-6 py-4">
                <div class="flex items-center gap-3">
                  <div
                    class="w-8 h-8 rounded-xl bg-gray-100 flex items-center justify-center text-sm font-bold text-gray-600"
                  >
                    {{ p.nama.charAt(0) }}
                  </div>
                  <span class="text-sm font-medium text-gray-900">{{
                    p.nama
                  }}</span>
                </div>
              </td>
              <td v-for="k in kriteriaList" :key="k.id" class="px-4 py-4">
                <input
                  :value="getNilai(p.id, k.id)"
                  @input="
                    setNilai(
                      p.id,
                      k.id,
                      ($event.target as HTMLInputElement).value,
                    )
                  "
                  type="number"
                  min="0"
                  placeholder="0"
                  class="w-full border border-gray-200 rounded-xl px-3 py-2 text-sm outline-none focus:border-gray-900 transition-all text-center"
                />
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>

    <div v-else class="flex items-center justify-center py-20">
      <div
        class="w-6 h-6 border-2 border-gray-900 border-t-transparent rounded-full animate-spin"
      />
    </div>

    <Transition name="fade">
      <div
        v-if="showSuccess"
        class="fixed bottom-6 right-6 bg-gray-900 text-white px-5 py-3 rounded-2xl text-sm font-medium shadow-lg flex items-center gap-2"
      >
        ✅ Penilaian berhasil disimpan!
      </div>
    </Transition>
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

interface UpsertItem {
  provider_id: string;
  kriteria_id: string;
  nilai: number;
}

const loading = ref(true);
const saving = ref(false);
const showSuccess = ref(false);
const kriteriaList = ref<Kriteria[]>([]);
const providerList = ref<Provider[]>([]);
const nilaiMatrix = ref<Map<string, Map<string, string>>>(new Map());

function makeKey(providerId: string, kriteriaId: string): string {
  return `${providerId}__${kriteriaId}`;
}

function getNilai(providerId: string, kriteriaId: string): string {
  return nilaiMatrix.value.get(providerId)?.get(kriteriaId) ?? "";
}

function setNilai(providerId: string, kriteriaId: string, val: string): void {
  let providerRow = nilaiMatrix.value.get(providerId);
  if (!providerRow) {
    providerRow = new Map();
    nilaiMatrix.value.set(providerId, providerRow);
  }
  providerRow.set(kriteriaId, val);
}

async function fetchData() {
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

  kriteriaList.value = (kriteriaRaw as Kriteria[]) ?? [];
  providerList.value = (providerRaw as Provider[]) ?? [];

  const matrix = new Map<string, Map<string, string>>();

  for (const p of providerList.value) {
    const providerRow = new Map<string, string>();
    for (const k of kriteriaList.value) {
      providerRow.set(k.id, "");
    }
    matrix.set(p.id, providerRow);
  }

  const nilaiList = (nilaiRaw as NilaiProvider[]) ?? [];
  for (const n of nilaiList) {
    const providerRow = matrix.get(n.provider_id);
    if (providerRow !== undefined) {
      providerRow.set(n.kriteria_id, String(n.nilai));
    }
  }

  nilaiMatrix.value = matrix;
  loading.value = false;
}

async function simpanSemua() {
  saving.value = true;

  const upsertData: UpsertItem[] = [];

  for (const p of providerList.value) {
    const providerRow = nilaiMatrix.value.get(p.id);
    if (!providerRow) continue;

    for (const k of kriteriaList.value) {
      const val = providerRow.get(k.id);
      if (val !== undefined && val !== "") {
        upsertData.push({
          provider_id: p.id,
          kriteria_id: k.id,
          nilai: Number(val),
        });
      }
    }
  }

  await $supabase.from("nilai_provider").upsert(upsertData, {
    onConflict: "provider_id,kriteria_id",
  });

  saving.value = false;
  showSuccess.value = true;
  setTimeout(() => (showSuccess.value = false), 3000);
}

onMounted(fetchData);
</script>

<style scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}
</style>
