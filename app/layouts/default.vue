<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Sidebar -->
    <aside
      class="fixed top-0 left-0 h-full w-64 bg-white border-r border-gray-100 flex flex-col z-10"
    >
      <!-- Logo -->
      <div class="px-6 py-6 border-b border-gray-100">
        <h1 class="text-lg font-bold text-gray-900">TOPSIS</h1>
        <p class="text-xs text-gray-400 mt-0.5">Pemilihan Provider Terbaik</p>
      </div>

      <!-- Navigation -->
      <nav class="flex-1 px-4 py-6 space-y-1">
        <NuxtLink
          v-for="item in navItems"
          :key="item.to"
          :to="item.to"
          class="flex items-center gap-3 px-3 py-2.5 rounded-xl text-sm font-medium transition-all"
          :class="
            $route.path === item.to
              ? 'bg-gray-900 text-white'
              : 'text-gray-500 hover:bg-gray-50 hover:text-gray-900'
          "
        >
          <span class="text-base">{{ item.icon }}</span>
          {{ item.label }}
        </NuxtLink>
      </nav>

      <!-- Footer -->
      <div class="px-6 py-4 border-t border-gray-100">
        <p class="text-xs text-gray-400">Sistem Pengambil Keputusan</p>
      </div>
    </aside>

    <!-- Main Content -->
    <main class="ml-64 p-8">
      <slot />
    </main>
  </div>
</template>

<script setup lang="ts">
const navItems = [
  { to: "/", icon: "🏠", label: "Dashboard" },
  { to: "/kriteria", icon: "⚖️", label: "Kriteria & Bobot" },
  { to: "/provider", icon: "📡", label: "Provider" },
  { to: "/penilaian", icon: "📝", label: "Penilaian" },
  { to: "/hasil", icon: "🏆", label: "Hasil TOPSIS" },
];
</script>
