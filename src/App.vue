<template>
  <div class="app">
    <timer-tab @add-log="addLog" />
    <log-list 
      :logs="sortedLogs"
      :current-date="formatDate(new Date())" 
      @delete-log="deleteLog"
      @update-log="updateLog"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import TimerTab from './components/TimerTab.vue'
import LogList from './components/LogList.vue'
import { ILog } from './models/log';

export default defineComponent({
  name: 'App',
  components: {
    TimerTab,
    LogList
  },
  data() {
    return {
      logs: [
        {
          id: 164241320,
          activity: 'Training',
          category: 'Sport',
          date: '24.03.2021',
          time: '00:32:33'
        },
        {
          id: 16424233,
          activity: 'Test Activity 1',
          category: 'My Category',
          date: '24.03.2022',
          time: '10:00:39'
        },
        {
          id: 164241212,
          activity: 'Client Meeting',
          category: 'Job',
          date: '24.03.2021',
          time: '00:03:47'
        }
      ] as Array<ILog>
    }
  },
  computed: {
    sortedDates(): Array<Date> {
      const uniqueDates = [...new Set(this.logs.map(({ date }) => date))];
      const parsedDates: Array<Date> = uniqueDates.map((date: string) => {
        const parts: Array<string> = date.split('.');
        return new Date(parseInt(parts[2]), parseInt(parts[1]) - 1, parseInt(parts[0]))
      })
      return parsedDates.sort((dateA, dateB) => {
        return new Date(dateB).getTime() - new Date(dateA).getTime();
      });
    },
    sortedDatesFormatted(): Array<string> {
      return this.sortedDates.map(this.formatDate);
    },
    sortedLogs(): Array<{ date: string, logs: Array<ILog> }> {
      return this.sortedDatesFormatted.map(date => {
        const logs = this.logs.filter(log => log.date === date);
        return {
          date,
          logs
        }
      })
    }
  },
  watch: {
    logs: {
      deep: true,
      handler(value) {
        window.localStorage.setItem('logs', JSON.stringify(value));
      }
    }
  },
  mounted() {
    const logs: string | null = window.localStorage.getItem('logs');
    if (logs) {
      this.logs = JSON.parse(logs);
    }
  },
  methods: {
    formatDate(dateToFormat: Date): string {
      const date = new Date(dateToFormat);
      const day = String(date.getDate()).padStart(2, '0');
      const month = String(date.getMonth() + 1).padStart(2, '0');
      const year = date.getFullYear();
      return day + '.' + month + '.' + year;
    },
    addLog(log: ILog): void {
      this.logs.unshift({
        ...log,
        date: this.formatDate(new Date())
      });
    },
    deleteLog(logId: number): void {
      this.logs = this.logs.filter(({ id }) => id !== logId);
    },
    updateLog(log: ILog): void {
      this.logs = this.logs.map((logObj: ILog) => {
        if (logObj.id === log.id) {
          return { ...log };
        } 
        return logObj;
      });
    }
  }
});
</script>

<style>
.app {
  margin: 0 auto;
  max-width: 800px;
  min-width: 700px;
}
</style>
