<template>
    <div class="machine-list-container">
      <div class="machine-list">
        <div
          v-for="machine in machines"
          :key="machine.id"
          class="machine-item"
        >
          <MachineStatus
            :machine-id="machine.id"
            :status="machine.status"
            :countdown="machine.countdown"
            @update-status="updateMachineStatus"
          />
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import MachineStatus from './MachineStatus.vue';
  
  export default {
    components: { MachineStatus },
    props: {
      machines: Array
    },
    methods: {
      updateMachineStatus(id, status, countdown) {
        this.$emit('update-status', id, status, countdown);
      }
    }
  };
  </script>
  
  <style scoped>
  .machine-list-container {
    display: flex;
    justify-content: center; /* จัดให้เนื้อหาอยู่ตรงกลางในแนวนอน */
    align-items: flex-start; /* ชิดด้านบน */
    padding: 16px;
    min-height: 100vh;
  }
  
  .machine-list {
    display: flex;
    flex-wrap: wrap;
    gap: 24px;
    justify-content: center; /* จัดให้การ์ดอยู่ตรงกลาง */
    align-content: flex-start; /* จัดให้การ์ดทั้งหมดชิดด้านบน */
  }
  </style>
  