// Code by [Risqi]
<template>
  <div id="app">
    <HeaderNav
      :currentRole="currentRole"
      @update-role="updateRole"
      @toggle-sidebar="toggleSidebar"
      :isSidebarVisible="isSidebarVisible"
    />
    <div class="app-content" :class="{ noHeader: !showHeader}">
      <SidebarNav
      v-if="showSidebar"
      :currentRole="currentRole"
      :isSidebarVisible="isSidebarVisible"
      @showComponent="navigateTo"
    />
      <div class="main-content" :class="{ expanded: isSidebarVisible && showSidebar }">
        <router-view :key="$route.fullPath" :currentComponent="$route.params.component"/>
      </div>
    </div>
  </div>
</template>

<script>
import HeaderNav from "./components/dashboard/HeaderNav.vue";
import SidebarNav from "./components/dashboard/SidebarNav.vue";
import { EventBus } from '@/utils/EventBus'; 

export default {
  components: {
    HeaderNav,
    SidebarNav,
  },
  data() {
    return {
      currentRole: localStorage.getItem("role") || "guest",
      isSidebarVisible: true,
      searchTerm: "",
    };
  },
  computed: {
    showHeader() {
      return !this.$route.meta.hideHeader;
    },
    showSidebar() {
      return !this.$route.meta.hideSidebar;
    },
  },
  watch:  {
    "$route.name"(newRole) {
      this.currentRole = newRole;
    },
  },
  methods: {
    updateRole(role) {
      this.currentRole = role;
    },
    navigateTo(component ){
      if (this.currentRole === "ADMIN") {
        this.$router.push({ name: this.currentRole, params: { component } });
      } else if (this.currentRole === "USER") {
        this.$router.push({ name: "user" });
      } else {
        this.$router.push({ name: "login" });
      }
    },
    toggleSidebar() {
      this.isSidebarVisible = !this.isSidebarVisible;
    },
    handleSearch(newQuery) {
      console.log("Search term:", newQuery);
      if (this.currentRole === "admin") {
        console.log("Search in admin items");
      }  else if (this.currentRole === "user") {
        console.log("Search in user items");
      }
    },
  },
  mounted() {
    EventBus.on('search', this.handleSearch);
  },
  beforeUnmount() {
    EventBus.off('search', this.handleSearch);
  },
};
</script>

<style scoped>
html, body {
  height: 100%;
  margin: 0;
  background-color: #4b3f6b;
}
#app {
  display: flex;
  flex-direction: column;
  height: 100%;
  background-color: #4b3f6b;
}
.app-content {
  display: flex;
  height: calc(100vh - 60px);
  flex-grow: 1;
  font-family: Avenir, Helvetica, Arial, sans-serif;
  font: 1em sans-serif;
  margin-top: 60px;
  background-color: #4b3f6b;
}
.main-content {
  flex-grow: 1;
  padding: 20px;
  transition: margin-left 0.3s ease;
}
.main-content.expanded {
  margin-left: 200px;
}
.app-content.noHeader {
  margin-top: 0;
  height: 100vh;
}
@media (max-width: 768px) {
  .main-content {
    margin-left: 0;
    margin-top: 180px;
  }
  .app-content.noHeader {
    margin-top: 0;
    height: calc(100vh - 60px);
  }
}
</style>