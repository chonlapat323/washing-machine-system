<template>
    <a-card
      :title="`Machine ${machineId}`"
      class="machine-card"
      :body-style="{ padding: '16px' }"
    >
      <p>Status: {{ status }}</p>
      <p v-if="status === 'in use'">
        Time Remaining: {{ minutes }}:{{ seconds }}
      </p>
      <a-button 
        type="primary" 
        @click="startMachine" 
        v-if="status === 'available'"
      >
        Start Machine
      </a-button>
    </a-card>
  </template>

  
<script>
export default {
  props: ['machineId', 'status', 'countdown'],
  data() {
    return {
      localCountdown: this.countdown, // ใช้ countdown เป็นวินาทีโดยตรง
      intervalId: null,
      notificationSent: false // ใช้เพื่อตรวจสอบว่าส่งสัญญาณแจ้งเตือนแล้วหรือไม่
    };
  },
  computed: {
    minutes() {
      return Math.floor(this.localCountdown / 60);
    },
    seconds() {
      return String(this.localCountdown % 60).padStart(2, '0');
    }
  },
  methods: {
    startMachine() {
      this.localCountdown = 1.1 * 60; // ตั้งค่าเริ่มต้นเป็น 15 นาที (900 วินาที)
      this.$emit('update-status', this.machineId, 'in use', this.localCountdown); // ส่งข้อมูลเพื่ออัปเดตทันที
      this.startCountdown();
    },
    startCountdown() {
      if (this.intervalId) return; // ป้องกันการจับเวลาซ้ำซ้อน
      this.intervalId = setInterval(() => {
        if (this.localCountdown > 0) {
          this.localCountdown -= 1;
           // ตรวจสอบหากเวลาเหลือ 60 วินาที และยังไม่ได้ส่งสัญญาณแจ้งเตือน
           if (this.localCountdown === 60 && !this.notificationSent) {
            console.log('noti')
            this.sendNotification();
            this.notificationSent = true; // ป้องกันการส่งซ้ำ
          }
          this.$emit('update-status', this.machineId, 'in use', this.localCountdown); // ส่งค่า countdown เป็นวินาที
        } else {
          this.stopCountdown();
          this.$emit('update-status', this.machineId, 'available', 0);
        }
      }, 1000);
    },
    stopCountdown() {
      clearInterval(this.intervalId);
      this.intervalId = null;
      this.notificationSent = false; // รีเซ็ตการแจ้งเตือนเมื่อเริ่มใหม่
    },
    sendNotification() {
      fetch('http://localhost:3000/send-notify', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ message: `Machine ${this.machineId} has only 1 minute remaining!` })
      })
      .then(response => response.json())
      .then(data => {
        if (data.success) {
          console.log('Notification sent successfully');
        } else {
          console.error('Failed to send notification');
        }
      })
      .catch(error => {
        console.error('Error:', error);
      });
    }
  },
  mounted() {
    if (this.status === 'in use' && this.localCountdown > 0) {
      this.startCountdown();
    }
  },
  beforeUnmount() {
    this.stopCountdown();
  },
  watch: {
    countdown(newCountdown) {
      this.localCountdown = newCountdown; // อัปเดตค่าใหม่ในท้องถิ่นทุกครั้งที่ App.vue ส่งค่าใหม่มา
    }
  }
};
</script>

  
  
  <style scoped>
  .machine-card {
    width: 250px; /* กำหนดความกว้างคงที่ */
    text-align: center;
    margin-bottom: 24px;
    border-radius: 8px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  }
    ::v-deep .ant-card-body {
        min-height: 110px;
    }
  </style>
  