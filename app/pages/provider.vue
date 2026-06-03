<template>
  <div class="space-y-6">
    <div class="flex items-center justify-between">
      <div>
        <h1 class="text-xl lg:text-2xl font-bold text-gray-900">Provider</h1>
        <p class="text-gray-400 text-sm mt-1">
          Kelola daftar provider sebagai alternatif pilihan
        </p>
      </div>
      <button
        @click="showModal = true"
        class="flex items-center gap-2 bg-gray-900 text-white px-3 lg:px-4 py-2.5 rounded-xl text-sm font-medium hover:bg-gray-700 transition-all"
      >
        <span>+</span>
        <span class="hidden sm:inline">Tambah Provider</span>
        <span class="sm:hidden">Tambah</span>
      </button>
    </div>

    <div v-if="loading" class="flex items-center justify-center py-20">
      <div
        class="w-6 h-6 border-2 border-gray-900 border-t-transparent rounded-full animate-spin"
      />
    </div>

    <div
      v-else-if="providerList.length === 0"
      class="flex flex-col items-center justify-center py-20 text-center bg-white rounded-2xl border border-gray-100"
    >
      <span class="text-4xl mb-3">📡</span>
      <p class="text-gray-500 font-medium">Belum ada provider</p>
      <p class="text-gray-400 text-sm mt-1">
        Klik "Tambah Provider" untuk memulai
      </p>
    </div>

    <div v-else class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-4">
      <div
        v-for="(item, i) in providerList"
        :key="item.id"
        class="bg-white rounded-2xl border border-gray-100 p-5 space-y-4 hover:shadow-sm transition-all"
      >
        <div class="flex items-start justify-between">
          <div
            class="w-11 h-11 rounded-2xl bg-gray-100 flex items-center justify-center text-lg font-bold text-gray-600"
          >
            {{ item.nama.charAt(0).toUpperCase() }}
          </div>
          <span
            class="text-xs text-gray-400 bg-gray-50 px-2.5 py-1 rounded-full"
            >#{{ i + 1 }}</span
          >
        </div>
        <div>
          <h3 class="font-semibold text-gray-900">{{ item.nama }}</h3>
          <p class="text-sm text-gray-400 mt-1 line-clamp-2">
            {{ item.deskripsi || "Tidak ada deskripsi" }}
          </p>
        </div>
        <div class="flex gap-2 pt-2 border-t border-gray-50">
          <button
            @click="openEdit(item)"
            class="flex-1 text-xs text-gray-500 hover:text-gray-900 py-2 rounded-lg hover:bg-gray-50 transition-all"
          >
            Edit
          </button>
          <button
            @click="hapusProvider(item.id)"
            class="flex-1 text-xs text-red-400 hover:text-red-600 py-2 rounded-lg hover:bg-red-50 transition-all"
          >
            Hapus
          </button>
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
            {{ editMode ? "Edit Provider" : "Tambah Provider" }}
          </h2>

          <div class="space-y-1.5">
            <label class="text-xs font-medium text-gray-600"
              >Nama Provider</label
            >
            <input
              v-model="form.nama"
              type="text"
              placeholder="contoh: Telkomsel, Indosat, XL..."
              class="w-full border border-gray-200 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-gray-900 transition-all"
            />
          </div>

          <div class="space-y-1.5">
            <label class="text-xs font-medium text-gray-600"
              >Deskripsi <span class="text-gray-400">(opsional)</span></label
            >
            <textarea
              v-model="form.deskripsi"
              placeholder="Deskripsi singkat tentang provider..."
              rows="3"
              class="w-full border border-gray-200 rounded-xl px-4 py-2.5 text-sm outline-none focus:border-gray-900 transition-all resize-none"
            />
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
              @click="simpanProvider"
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

interface Provider {
  id: string;
  nama: string;
  deskripsi: string;
}

const loading = ref(true);
const saving = ref(false);
const showModal = ref(false);
const editMode = ref(false);
const errorMsg = ref("");
const providerList = ref<Provider[]>([]);
const form = ref({ id: "", nama: "", deskripsi: "" });

async function fetchProvider() {
  loading.value = true;
  const { data } = await $supabase
    .from("provider")
    .select("*")
    .order("created_at");
  providerList.value = data ?? [];
  loading.value = false;
}

function openEdit(item: Provider) {
  editMode.value = true;
  form.value = { id: item.id, nama: item.nama, deskripsi: item.deskripsi };
  showModal.value = true;
}

function closeModal() {
  showModal.value = false;
  editMode.value = false;
  errorMsg.value = "";
  form.value = { id: "", nama: "", deskripsi: "" };
}

async function simpanProvider() {
  errorMsg.value = "";
  if (!form.value.nama.trim())
    return (errorMsg.value = "Nama provider wajib diisi");
  saving.value = true;

  if (editMode.value) {
    await $supabase
      .from("provider")
      .update({ nama: form.value.nama, deskripsi: form.value.deskripsi })
      .eq("id", form.value.id);
  } else {
    await $supabase
      .from("provider")
      .insert({ nama: form.value.nama, deskripsi: form.value.deskripsi });
  }

  saving.value = false;
  closeModal();
  fetchProvider();
}

async function hapusProvider(id: string) {
  if (!confirm("Yakin ingin menghapus provider ini?")) return;
  await $supabase.from("provider").delete().eq("id", id);
  fetchProvider();
}

onMounted(fetchProvider);
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
