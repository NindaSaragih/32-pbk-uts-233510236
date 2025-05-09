<template>
  <div class="container">
    <h1>List Kegiatan Hari Ini</h1>
    
    <div class="date-time">
      <p>{{ currentDateTime }}</p>
    </div>

    <div class="input-section">
      <input v-model="newTask" placeholder="Tambah kegiatan baru" @keyup.enter="addTask" />
      <button @click="addTask">Tambah</button>
    </div>

    <div class="filter-box">
      <label><input type="radio" value="all" v-model="filter" /> Semua Kegiatan</label>
      <label><input type="radio" value="active" v-model="filter" /> Yang Belum Selesai</label>
      <label><input type="radio" value="completed" v-model="filter" /> Yang Sudah Selesai</label>
    </div>

    <ul class="task-list">
      <li v-for="(task, index) in filteredTasks" :key="index">
        <label>
          <input type="checkbox" v-model="task.done" />
          <span :class="{ done: task.done }">{{ task.text }}</span>
        </label>
        <button @click="deleteTask(index)">Hapus</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTask: '',
      filter: 'all',
      tasks: [],
      currentDateTime: '',
      timer: null
    }
  },
  computed: {
    filteredTasks() {
      if (this.filter === 'active') {
        return this.tasks.filter(task => !task.done)
      } else if (this.filter === 'completed') {
        return this.tasks.filter(task => task.done)
      }
      return this.tasks
    }
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        this.tasks.push({ text: this.newTask, done: false });
        this.newTask = '';
      }
    },
    deleteTask(index) {
      this.tasks.splice(index, 1);
    },
    updateDateTime() {
      const now = new Date();
      
      // Array nama hari dalam Bahasa Indonesia
      const hariArray = ['Minggu', 'Senin', 'Selasa', 'Rabu', 'Kamis', 'Jumat', 'Sabtu'];
      
      // Array nama bulan dalam Bahasa Indonesia
      const bulanArray = ['Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni', 'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'];
      
      const hari = hariArray[now.getDay()];
      const tanggal = now.getDate();
      const bulan = bulanArray[now.getMonth()];
      const tahun = now.getFullYear();
      
      // Format jam dengan leading zero jika perlu
      const jam = String(now.getHours()).padStart(2, '0');
      const menit = String(now.getMinutes()).padStart(2, '0');
      const detik = String(now.getSeconds()).padStart(2, '0');
      
      this.currentDateTime = `${hari}, ${tanggal} ${bulan} ${tahun} - ${jam}:${menit}:${detik}`;
    }
  },
  mounted() {
    // Update waktu secara real-time setiap detik
    this.updateDateTime();
    this.timer = setInterval(this.updateDateTime, 1000);
  },
  beforeUnmount() {
    // Bersihkan timer saat komponen dihancurkan
    clearInterval(this.timer);
  }
}
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

h1 {
  text-align: center;
  color: #333;
}

.date-time {
  text-align: center;
  font-size: 1.2rem;
  margin-bottom: 30px;
  padding: 10px;
  background-color: #7bd6f2;
  border-radius: 5px;
  font-weight: bold;
  max-width: 600px;
}

.input-section {
  display: flex;
  margin-bottom: 20px;
}

.input-section input {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px 0 0 4px;
  font-size: 16px;
}

.input-section button {
  padding: 10px 20px;
  background-color: #419bfb;
  color: white;
  border: none;
  border-radius: 0 4px 4px 0;
  cursor: pointer;
  font-size: 16px;
}

.filter-box {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}

.task-list {
  list-style-type: none;
  padding: 0;
}

.task-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  margin-bottom: 8px;
  background-color: #f9f9f9;
  border-radius: 4px;
}

.task-list button {
  padding: 5px 10px;
  background-color: #f44336;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.done {
  text-decoration: line-through;
  color: #888;
}
</style>