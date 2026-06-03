<template>
  <div class="space-y-6">
    <div class="flex items-center justify-between">
      <div>
        <h1 class="text-xl lg:text-2xl font-bold text-gray-900">
          Kriteria & Bobot
        </h1>
        <p class="text-gray-400 text-sm mt-1">
          Kelola kriteria dan bobot penilaian provider
        </p>
      </div>
      <button
        @click="showModal = true"
        class="flex items-center gap-2 bg-gray-900 text-white px-3 lg:px-4 py-2.5 rounded-xl text-sm font-medium hover:bg-gray-700 transition-all"
      >
        <span>+</span>
        <span class="hidden sm:inline">Tambah Kriteria</span>
        <span class="sm:hidden">Tambah</span>
      </button>
    </div>

    <div
      class="flex items-center gap-3 px-4 py-3 rounded-xl text-sm"
      :class="
        totalBobot === 1
          ? 'bg-green-50 text-green-700'
          : 'bg-yellow-50 text-yellow-700'
      "
    >
      <span>{{ totalBobot === 1 ? "✅" : "⚠️" }}</span>
      <span>
        Total bobot: <strong>{{ (totalBobot * 100).toFixed(0) }}%</strong>
        {{ totalBobot === 1 ? "— Valid!" : "— Harus tepat 100%" }}
      </span>
    </div>

    <div class="bg-white rounded-2xl border border-gray-100 overflow-hidden">
      <div v-if="loading" class="flex items-center justify-center py-20">
        <div
          class="w-6 h-6 border-2 border-gray-900 border-t-transparent rounded-full animate-spin"
        />
      </div>

      <div
        v-else-if="kriteriaList.length === 0"
        class="flex flex-col items-center justify-center py-20 text-center"
      >
        <span class="text-4xl mb-3">⚖️</span>
        <p class="text-gray-500 font-medium">Belum ada kriteria</p>
        <p class="text-gray-400 text-sm mt-1">
          Klik "Tambah Kriteria" untuk memulai
        </p>
      </div>

      <!-- Desktop Table -->
      <table v-else class="hidden sm:table w-full">
        <thead class="bg-gray-50 border-b border-gray-100">
          <tr>
            <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
              No
            </th>
            <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
              Nama Kriteria
            </th>
            <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
              Bobot
            </th>
            <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
              Jenis
            </th>
            <th class="text-left text-xs font-medium text-gray-400 px-6 py-4">
              Aksi
            </th>
          </tr>
        </thead>
        <tbody class="divide-y divide-gray-50">
          <tr
            v-for="(item, i) in kriteriaList"
            :key="item.id"
            class="hover:bg-gray-50 transition-colors"
          >
            <td class="px-6 py-4 text-sm text-gray-400">{{ i + 1 }}</td>
            <td class="px-6 py-4 text-sm font-medium text-gray-900">
              {{ item.nama }}
            </td>
            <td class="px-6 py-4">
              <div class="flex items-center gap-3">
                <div class="flex-1 max-w-24 bg-gray-100 rounded-full h-1.5">
                  <div
                    class="bg-gray-900 h-1.5 rounded-full"
                    :style="{ width: item.bobot * 100 + '%' }"
                  />
                </div>
                <span class="text-sm text-gray-700 font-medium"
                  >{{ (item.bobot * 100).toFixed(0) }}%</span
                >
              </div>
            </td>
            <td class="px-6 py-4">
              <span
                class="text-xs font-medium px-2.5 py-1 rounded-full"
                :class="
                  item.jenis === 'benefit'
                    ? 'bg-green-50 text-green-700'
                    : 'bg-red-50 text-red-700'
                "
              >
                {{ item.jenis === "benefit" ? "↑ Benefit" : "↓ Cost" }}
              </span>
            </td>
            <td class="px-6 py-4">
              <div class="flex items-center gap-2">
                <button
                  @click="openEdit(item)"
                  class="text-xs text-gray-500 hover:text-gray-900 px-3 py-1.5 rounded-lg hover:bg-gray-100 transition-all"
                >
                  Edit
                </button>
                <button
                  @click="hapusKriteria(item.id)"
                  class="text-xs text-red-400 hover:text-red-600 px-3 py-1.5 rounded-lg hover:bg-red-50 transition-all"
                >
                  Hapus
                </button>
              </div>
            </td>
          </tr>
        </tbody>
      </table>

      <!-- Mobile Cards -->
      <div
        v-if="kriteriaList.length > 0"
        class="sm:hidden divide-y divide-gray-50"
      >
        <div
          v-for="(item, i) in kriteriaList"
          :key="item.id"
          class="p-4 space-y-3"
        >
          <div class="flex items-start justify-between">
            <div>
              <p class="text-sm font-medium text-gray-900">{{ item.nama }}</p>
              <span
                class="text-xs font-medium px-2 py-0.5 rounded-full mt-1 inline-block"
                :class="
                  item.jenis === 'benefit'
                    ? 'bg-green-50 text-green-700'
                    : 'bg-red-50 text-red-700'
                "
              >
                {{ item.jenis === "benefit" ? "↑ Benefit" : "↓ Cost" }}
              </span>
            </div>
            <span
              class="text-xs text-gray-400 bg-gray-50 px-2 py-1 rounded-full"
              >#{{ i + 1 }}</span
            >
          </div>
          <div class="flex items-center gap-3">
            <div class="flex-1 bg-gray-100 rounded-full h-1.5">
              <div
                class="bg-gray-900 h-1.5 rounded-full"
                :style="{ width: item.bobot * 100 + '%' }"
              />
            </div>
            <span class="text-sm font-medium text-gray-700"
              >{{ (item.bobot * 100).toFixed(0) }}%</span
            >
          </div>
          <div class="flex gap-2">
            <button
              @click="openEdit(item)"
              class="flex-1 text-xs text-gray-500 hover:text-gray-900 py-2 rounded-lg hover:bg-gray-100 transition-all border border-gray-100"
            >
              Edit
            </button>
            <button
              @click="hapusKriteria(item.id)"
              class="flex-1 text-xs text-red-400 hover:text-red-600 py-2 rounded-lg hover:bg-red-50 transition-all border border-red-50"
            >
              Hapus
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal -->
    <Transition name="fade">
      <div
        v-if="showModal"
        class="fixed inset-0 bg-black/30 backdrop-blur-sm flex items-end sm:items-center justify-center z-50 p-4"
        @click.self="closeModal"
      >
        <div
          class="bg-white rounded-2xl shadow-xl w-full max-w-md p-6 space-y-5"
        >
          <h2 class="text-base font-semibold text-gray-900">
            {{ editMode ? "Edit Kriteria" : "Tambah Kriteria" }}
          </h2>

          <div class="space-y-1.5">
            <label class="text-xs font-medium text-gray-600"
              >Nama Kriteria</label
            >
            <input
              v-model="form.nama"
              type="text"
              placeholder="contoh: Harga, Kecepatan, Coverage..."
              class="w-full border border-gray-200 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-gray-900 transition-all"
            />
          </div>

          <div class="space-y-1.5">
            <label class="text-xs font-medium text-gray-600"
              >Bobot <span class="text-gray-400">(0 - 100%)</span></label
            >
            <div class="relative">
              <input
                v-model="form.bobot"
                type="number"
                min="1"
                max="100"
                placeholder="contoh: 25"
                class="w-full border border-gray-200 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-gray-900 transition-all pr-10"
              />
              <span
                class="absolute right-4 top-1/2 -translate-y-1/2 text-sm text-gray-400"
                >%</span
              >
            </div>
            <p class="text-xs text-gray-400">
              Sisa bobot tersedia: <strong>{{ sisaBobot }}%</strong>
            </p>
          </div>

          <div class="space-y-1.5">
            <label class="text-xs font-medium text-gray-600"
              >Jenis Kriteria</label
            >
            <div class="grid grid-cols-2 gap-3">
              <button
                @click="form.jenis = 'benefit'"
                class="flex flex-col items-center gap-1 border-2 rounded-xl py-3 text-sm font-medium transition-all"
                :class="
                  form.jenis === 'benefit'
                    ? 'border-green-500 bg-green-50 text-green-700'
                    : 'border-gray-100 text-gray-400 hover:border-gray-200'
                "
              >
                <span>↑</span> Benefit
                <span class="text-xs font-normal"
                  >Lebih tinggi = lebih baik</span
                >
              </button>
              <button
                @click="form.jenis = 'cost'"
                class="flex flex-col items-center gap-1 border-2 rounded-xl py-3 text-sm font-medium transition-all"
                :class="
                  form.jenis === 'cost'
                    ? 'border-red-500 bg-red-50 text-red-700'
                    : 'border-gray-100 text-gray-400 hover:border-gray-200'
                "
              >
                <span>↓</span> Cost
                <span class="text-xs font-normal"
                  >Lebih rendah = lebih baik</span
                >
              </button>
            </div>
          </div>

          <p v-if="errorMsg" class="text-xs text-red-500">{{ errorMsg }}</p>

          <div class="flex gap-3 pt-2">
            <button
              @click="closeModal"
              class="flex-1 border border-gray-200 text-gray-600 py-2.5 rounded-xl text-sm font-medium hover:bg-gray-50 transition-all"
            >
              Batal
            </button>
            <button
              @click="simpanKriteria"
              :disabled="saving"
              class="flex-1 bg-gray-900 text-white py-2.5 rounded-xl text-sm font-medium hover:bg-gray-700 transition-all disabled:opacity-50"
            >
              {{ saving ? "Menyimpan..." : editMode ? "Update" : "Simpan" }}
            </button>
          </div>
        </div>
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
  jenis: "benefit" | "cost";
}

const loading = ref(true);
const saving = ref(false);
const showModal = ref(false);
const editMode = ref(false);
const errorMsg = ref("");
const kriteriaList = ref<Kriteria[]>([]);

const form = ref({
  id: "",
  nama: "",
  bobot: "",
  jenis: "benefit" as "benefit" | "cost",
});

const totalBobot = computed(() =>
  kriteriaList.value.reduce((sum, k) => sum + k.bobot, 0),
);

const sisaBobot = computed(() => {
  const used = editMode.value
    ? kriteriaList.value
        .filter((k) => k.id !== form.value.id)
        .reduce((sum, k) => sum + k.bobot, 0)
    : totalBobot.value;
  return Math.round((1 - used) * 100);
});

async function fetchKriteria() {
  loading.value = true;
  const { data } = await $supabase
    .from("kriteria")
    .select("*")
    .order("created_at");
  kriteriaList.value = data ?? [];
  loading.value = false;
}

function openEdit(item: Kriteria) {
  editMode.value = true;
  form.value = {
    id: item.id,
    nama: item.nama,
    bobot: String(Math.round(item.bobot * 100)),
    jenis: item.jenis,
  };
  showModal.value = true;
}

function closeModal() {
  showModal.value = false;
  editMode.value = false;
  errorMsg.value = "";
  form.value = { id: "", nama: "", bobot: "", jenis: "benefit" };
}

async function simpanKriteria() {
  errorMsg.value = "";
  const bobotNum = Number(form.value.bobot);
  if (!form.value.nama.trim())
    return (errorMsg.value = "Nama kriteria wajib diisi");
  if (!bobotNum || bobotNum <= 0)
    return (errorMsg.value = "Bobot harus lebih dari 0");
  if (bobotNum > sisaBobot.value)
    return (errorMsg.value = `Bobot melebihi sisa ${sisaBobot.value}%`);

  saving.value = true;
  const bobotDecimal = bobotNum / 100;

  if (editMode.value) {
    await $supabase
      .from("kriteria")
      .update({
        nama: form.value.nama,
        bobot: bobotDecimal,
        jenis: form.value.jenis,
      })
      .eq("id", form.value.id);
  } else {
    await $supabase
      .from("kriteria")
      .insert({
        nama: form.value.nama,
        bobot: bobotDecimal,
        jenis: form.value.jenis,
      });
  }

  saving.value = false;
  closeModal();
  fetchKriteria();
}

async function hapusKriteria(id: string) {
  if (!confirm("Yakin ingin menghapus kriteria ini?")) return;
  await $supabase.from("kriteria").delete().eq("id", id);
  fetchKriteria();
}

onMounted(fetchKriteria);
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
