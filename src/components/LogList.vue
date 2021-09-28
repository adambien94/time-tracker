<template>
  <ul class="log-list">
    <li v-for="(day, index) in logs" :key='`day-${index}`'>
      <div class="date-divider"> 
        <span class="date-divider__date">
          {{ day.date === currentDate ? 'Today' : day.date }}
        </span>
      </div> 
      <ul>
        <li v-for="log in day.logs" :key="`log-${log.id}`">
          <log-tab 
            :id="log.id"
            :activity="log.activity"
            :category="log.category"
            :time="log.time"
            :date="log.date"
            @delete-log="deleteLog"
            @update-log="updateLog"
          />
        </li>
      </ul>
    </li>
  </ul>
</template>

<script lang="ts">
import { defineComponent, PropType } from 'vue';
import LogTab from './LogTab.vue'
import { ILog } from '../models/log';

export default defineComponent({
  name: 'log-list',
  components: {
    LogTab
  },
  props: {
    logs: {
      type: Array as PropType<ILog[]>
    },
    currentDate: String
  },
  setup(_, context) {
    const deleteLog = (logId: number): void => {
      context.emit('deleteLog', logId);
    }

    const updateLog = (log: ILog): void => {
      context.emit('updateLog', log);
    }

    return {
      deleteLog,
      updateLog
    }
  }
});
</script>

<style>
.date-divider {
  position: relative;
  padding-bottom: 27px;
}

.date-divider:before {
  content: '';
  display: block;
  position: absolute;
  top: 50%;
  height: 1px;
  width: 100%;
  background: #dbdddf;
  z-index: -1;;
}

.date-divider__date {
  display: block;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  font-size: 13px;
  background: #e5e7eb;
  padding: 0 20px;
  font-size: 13px;
  font-weight: 500;
  color: #a5aeb9;
}
</style>
