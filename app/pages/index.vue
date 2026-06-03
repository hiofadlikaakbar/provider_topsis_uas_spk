<template>
  <div class="space-y-8">
    <!-- Header -->
    <div>
      <h1 class="text-2xl font-bold text-gray-900">Dashboard</h1>
      <p class="text-gray-400 text-sm mt-1">
        Selamat datang di Sistem Pemilihan Provider Terbaik
      </p>
    </div>

    <!-- Stats Cards -->
    <div class="grid grid-cols-3 gap-6">
      <div
        v-for="stat in stats"
        :key="stat.label"
        class="bg-white rounded-2xl p-6 border border-gray-100"
      >
        <div class="flex items-center justify-between mb-4">
          <span class="text-2xl">{{ stat.icon }}</span>
          <span
            class="text-xs font-medium px-2.5 py-1 rounded-full"
            :class="stat.badgeClass"
          >
            {{ stat.badge }}
          </span>
        </div>
        <p class="text-3xl font-bold text-gray-900">
          {{ loading ? "—" : stat.value }}
        </p>
        <p class="text-sm text-gray-400 mt-1">{{ stat.label }}</p>
      </div>
    </div>

    <!-- Panduan -->
    <div class="bg-white rounded-2xl border border-gray-100 p-6">
      <h2 class="text-base font-semibold text-gray-900 mb-5">
        Alur Penggunaan
      </h2>
      <div class="flex items-start gap-0">
        <div
          v-for="(step, i) in steps"
          :key="step.title"
          class="flex-1 flex flex-col items-center text-center relative"
        >
          <!-- Connector line -->
          <div
            v-if="i < steps.length - 1"
            class="absolute top-5 left-1/2 w-full h-px bg-gray-100"
          />
          <!-- Circle -->
          <div
            class="w-10 h-10 rounded-full flex items-center justify-center text-sm font-bold z-10 mb-3"
            :class="
              step.done ? 'bg-gray-900 text-white' : 'bg-gray-100 text-gray-400'
            "
          >
            {{ step.done ? "✓" : i + 1 }}
          </div>
          <p class="text-xs font-medium text-gray-700">{{ step.title }}</p>
          <p class="text-xs text-gray-400 mt-0.5 px-2">{{ step.desc }}</p>
        </div>
      </div>
    </div>

    <!-- Info TOPSIS -->
    <div class="bg-gray-900 rounded-2xl p-6 text-white">
      <h2 class="text-base font-semibold mb-2">Tentang Metode TOPSIS</h2>
      <p class="text-sm text-gray-400 leading-relaxed">
        <span class="text-white font-medium">TOPSIS</span> (Technique for Order
        Preference by Similarity to Ideal Solution) adalah metode pengambilan
        keputusan multi-kriteria yang memilih alternatif terbaik berdasarkan
        jarak terdekat ke solusi ideal positif dan jarak terjauh dari solusi
        ideal negatif.
      </p>
      <div class="grid grid-cols-2 gap-4 mt-5">
        <div class="bg-white/10 rounded-xl p-4">
          <p class="text-xs text-gray-400 mb-1">Solusi Ideal Positif</p>
          <p class="text-sm text-white">Nilai terbaik dari setiap kriteria</p>
        </div>
        <div class="bg-white/10 rounded-xl p-4">
          <p class="text-xs text-gray-400 mb-1">Solusi Ideal Negatif</p>
          <p class="text-sm text-white">Nilai terburuk dari setiap kriteria</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
definePageMeta({ layout: "default" });

const { $supabase } = useNuxtApp();

const loading = ref(true);
const jumlahKriteria = ref(0);
const jumlahProvider = ref(0);
const jumlahNilai = ref(0);

onMounted(async () => {
  const [k, p, n] = await Promise.all([
    $supabase.from("kriteria").select("*", { count: "exact", head: true }),
    $supabase.from("provider").select("*", { count: "exact", head: true }),
    $supabase
      .from("nilai_provider")
      .select("*", { count: "exact", head: true }),
  ]);

  jumlahKriteria.value = k.count ?? 0;
  jumlahProvider.value = p.count ?? 0;
  jumlahNilai.value = n.count ?? 0;
  loading.value = false;
});

const stats = computed(() => [
  {
    icon: "⚖️",
    label: "Total Kriteria",
    value: jumlahKriteria.value,
    badge: "Kriteria",
    badgeClass: "bg-blue-50 text-blue-600",
  },
  {
    icon: "📡",
    label: "Total Provider",
    value: jumlahProvider.value,
    badge: "Alternatif",
    badgeClass: "bg-purple-50 text-purple-600",
  },
  {
    icon: "📝",
    label: "Data Penilaian",
    value: jumlahNilai.value,
    badge: "Entri",
    badgeClass: "bg-green-50 text-green-600",
  },
]);

const steps = computed(() => [
  {
    title: "Input Kriteria",
    desc: "Tentukan kriteria & bobot",
    done: jumlahKriteria.value > 0,
  },
  {
    title: "Input Provider",
    desc: "Tambahkan alternatif provider",
    done: jumlahProvider.value > 0,
  },
  {
    title: "Beri Penilaian",
    desc: "Isi nilai tiap provider",
    done: jumlahNilai.value > 0,
  },
  {
    title: "Lihat Hasil",
    desc: "Ranking provider terbaik",
    done: false,
  },
]);
</script>
