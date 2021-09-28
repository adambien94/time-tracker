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
import { defineComponent, ref, computed, onMounted, watch } from 'vue';
import TimerTab from './components/TimerTab.vue'
import LogList from './components/LogList.vue'
import { ILog } from './models/log';

export default defineComponent({
  name: 'App',
  components: {
    TimerTab,
    LogList
  },
  setup() {
    const logs = ref<ILog[]>([{
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
      }]);

    const sortedDates = computed((): Date[] => {
      const uniqueDates = [...new Set(logs.value.map(({ date }) => date))];
      const parsedDates: Array<Date> = uniqueDates.map((date: string) => {
        const parts: Array<string> = date.split('.');
        return new Date(parseInt(parts[2]), parseInt(parts[1]) - 1, parseInt(parts[0]))
      })
      return parsedDates.sort((dateA, dateB) => {
        return new Date(dateB).getTime() - new Date(dateA).getTime();
      });
    })
    
    const sortedDatesFormatted = computed((): Array<string> => sortedDates.value.map(formatDate))

    const sortedLogs = computed((): Array<{ date: string, logs: Array<ILog> }> => {
      return sortedDatesFormatted.value.map(date => {
        const logsByDate: Array<ILog> = logs.value.filter(log => log.date === date);
        return {
          date,
          logs: logsByDate
        }
      })
    })

    const setLogs = (): void => {
      const logsFromStorage: string | null = window.localStorage.getItem('logs');
      if (logsFromStorage) {
        logs.value = JSON.parse(logsFromStorage);
      }
    }

    const formatDate = (dateToFormat: Date): string => {
      const date = new Date(dateToFormat);
      const day = String(date.getDate()).padStart(2, '0');
      const month = String(date.getMonth() + 1).padStart(2, '0');
      const year = date.getFullYear();
      return day + '.' + month + '.' + year;
    }

    const addLog = (log: ILog): void => {
      logs.value.unshift({
        ...log,
        date: formatDate(new Date())
      });
    }

    const deleteLog = (logId: number): void => {
      logs.value = logs.value.filter(({ id }) => id !== logId);
    }

    const updateLog = (log: ILog): void => {
      logs.value = logs.value.map((logObj: ILog) => {
        if (logObj.id === log.id) {
          return { ...log };
        } 
        return logObj;
      });
    }

    watch(logs, (value): void => {
        window.localStorage.setItem('logs', JSON.stringify(value));
      }, 
      { deep: true }
    )

    onMounted(setLogs)

    return {
      logs,
      sortedDates,
      sortedDatesFormatted,
      sortedLogs,
      formatDate,
      addLog,
      deleteLog,
      updateLog,
      setLogs
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
