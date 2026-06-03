<template>
  <div class="min-h-screen bg-gray-50">
    <!-- Mobile Header -->
    <header
      class="lg:hidden fixed top-0 left-0 right-0 z-20 bg-white border-b border-gray-100 px-4 py-3 flex items-center justify-between"
    >
      <div>
        <h1 class="text-base font-bold text-gray-900">TOPSIS</h1>
        <p class="text-xs text-gray-400">Pemilihan Provider Terbaik</p>
      </div>
      <button
        @click="menuOpen = !menuOpen"
        class="w-9 h-9 flex items-center justify-center rounded-xl hover:bg-gray-50 transition-all"
      >
        <span class="text-xl">{{ menuOpen ? "✕" : "☰" }}</span>
      </button>
    </header>

    <!-- Mobile Overlay -->
    <Transition name="fade">
      <div
        v-if="menuOpen"
        class="lg:hidden fixed inset-0 bg-black/30 z-30"
        @click="menuOpen = false"
      />
    </Transition>

    <!-- Sidebar -->
    <aside
      class="fixed top-0 left-0 h-full w-64 bg-white border-r border-gray-100 flex flex-col z-40 transition-transform duration-300"
      :class="menuOpen ? 'translate-x-0' : '-translate-x-full lg:translate-x-0'"
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
          @click="menuOpen = false"
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
    <main class="lg:ml-64 pt-16 lg:pt-0 p-4 lg:p-8">
      <slot />
    </main>
  </div>
</template>

<script setup lang="ts">
const menuOpen = ref(false);

const navItems = [
  { to: "/", icon: "🏠", label: "Dashboard" },
  { to: "/kriteria", icon: "⚖️", label: "Kriteria & Bobot" },
  { to: "/provider", icon: "📡", label: "Provider" },
  { to: "/penilaian", icon: "📝", label: "Penilaian" },
  { to: "/hasil", icon: "🏆", label: "Hasil TOPSIS" },
];
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
