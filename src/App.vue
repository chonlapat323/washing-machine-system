<template>
  <a-layout class="layout">
    <a-layout-header class="header">Washing Machine System</a-layout-header>
    
    <a-layout-content class="content">
      <MachineList v-if="isLoaded" :machines="machines" @update-status="updateMachineStatus" />
    </a-layout-content>

    <a-layout-footer class="footer">
      Washing Machine System ©2024 Created by Chonlapat
    </a-layout-footer>
  </a-layout>
</template>

<script>
import MachineList from './components/MachineList.vue';

export default {
  components: { MachineList },
  data() {
    return {
      machines: [
        { id: 1, status: 'available', countdown: 0 },
        { id: 2, status: 'available', countdown: 0 },
        { id: 3, status: 'available', countdown: 0 },
        { id: 4, status: 'available', countdown: 0 }
      ],
      isLoaded: false 
    };
  },
  methods: {
  saveMachineState() {
    localStorage.setItem('machines', JSON.stringify(this.machines));
  },
  loadMachineState() {
    const savedMachines = localStorage.getItem('machines');
    if (savedMachines) {
      this.machines = JSON.parse(savedMachines);
    }
    this.isLoaded = true;
  },
  updateMachineStatus(id, status, countdown) {
    const machine = this.machines.find(m => m.id === id);
    if (machine) {
      machine.status = status;
      machine.countdown = countdown; // บันทึกค่า countdown เป็นวินาทีโดยตรง
      this.saveMachineState(); // Save state to Local Storage
    }
  }
},
mounted() {
  this.loadMachineState();
}
};
</script>

<style scoped>
.layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

.header {
  color: white;
  background-color: #001529;
  text-align: center;
  font-size: 24px;
}

.content {
  flex: 1;
  display: flex;
  justify-content: center;
  align-items: flex-start;
}

.footer {
  text-align: center;
  background-color: #f0f2f5;
}
</style>
