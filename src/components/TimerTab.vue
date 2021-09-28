<template>
  <div class="timer-tab tab">
    <div class="timer-tab__input-wrapper">
      <input 
        class="timer-tab__activity-input"
        type="text" 
        placeholder="Add activity..*"
        v-model="activityName"
        :disabled="isTimerRunning"
      />
      <input 
        class="timer-tab__category-input"
        type="text" 
        placeholder="Add Category.."
        v-model="category"
        :disabled="isTimerRunning"
      />
    </div>
    <div class="timer-tab__timer">
      <span 
        :class="['timer__time', { 'timer__time--disabled': shouldStopWatchBeDisabled }]"
      >
        {{ formatedTime }}
      </span>
      <button 
        :disabled="shouldStopWatchBeDisabled"
        :class="['timer__btn', isTimerRunning ? 'timer__btn--stop' : 'timer__btn--play']" 
        @click="onTimerBtnClick"
      />
    </div>
    <button 
      class="timer-tab__add-btn" 
      :disabled="isTimerRunning || shouldStopWatchBeDisabled"
      @click="onAddLogBtnClick"
      >
        +
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed } from 'vue';

export default defineComponent({
  name: 'timer-tab',
  setup(_, context) {
    const activityName = ref<string>('');
    const category = ref<string>('');
    const currentTime = ref<number>(0);
    const formatedTime = ref<string>('00:00:00');
    const ticker = ref<undefined | number>(undefined);
    const interval = ref<number>(10);

    const isTimerRunning = computed((): boolean => !!ticker.value)
    const shouldStopWatchBeDisabled = computed((): boolean => !activityName.value.trim())

    const startWatch = (): void => {
      ticker.value = setInterval((): void => {
        currentTime.value++;
        formatedTime.value = formatTime(currentTime.value);
      }, interval.value);
    }

    const stopWatch = (): void => {
      clearInterval(ticker.value);
      ticker.value = undefined;
    }

    const onTimerBtnClick = (): void => {
      !ticker.value ? startWatch() : stopWatch(); 
    }

    const formatTime = (seconds: number): string => {
      const parsedTime: Date = new Date(0);
      parsedTime.setSeconds(seconds);
      return parsedTime.toISOString().substr(11, 8);
    }

    const onAddLogBtnClick = (): void => {
      addLog();
      clearTimer();
    }

    const addLog = (): void => {
      context.emit('addLog', {
        id: new Date().valueOf(),
        activity: activityName.value,
        category: category.value,
        time: formatedTime.value
      })
    }

    const clearTimer = (): void => {
      currentTime.value = 0;
      formatedTime.value = '00:00:00';
      activityName.value = '';
      category.value = '';
    }

    return {
      activityName,
      category,
      currentTime,
      formatedTime,
      ticker,
      interval,
      isTimerRunning,
      shouldStopWatchBeDisabled,
      startWatch,
      stopWatch,
      onTimerBtnClick,
      formatTime,
      onAddLogBtnClick,
      addLog,
      clearTimer
    }
  }
});
</script>

<style>
.timer-tab {
  display: flex;
  justify-content: space-between;
  position: relative;
}

.timer-tab__category-input {
  width: 155px;
  margin-left: 5px;
}

.timer-tab__input-wrapper {
  width: 400px;
  display: flex;
  justify-content: space-between;
  
}

.timer-tab__timer {
  width: 150px;
  display: flex;
  justify-content: end;
}

.timer__time {
  display: block;
  font-size: 19px;
  font-weight: 500;
  line-height: 35px;
}

.timer__time--disabled {
  color: rgba(0, 0, 0, 0.13);
}

.timer__btn {
  height: 35px;
  width: 35px;
  border-radius: 50%;
  border: none;
  margin-left: 20px; 
  cursor: pointer;
  position: relative;
}

.timer__btn:disabled {
  background: rgba(0, 0, 0, 0.09);
}

.timer__btn--play {
  background: #68d66d;
}

.timer__btn--play:before,
.timer__btn--stop:before {
  content: '';
  display: block;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background: #fff;
}

.timer__btn--play:before {
  width: 20px; 
  height: 20px; 
  clip-path: polygon(25% 14%, 25% 85%, 90% 52%);

}

.timer__btn--stop {
  background: #e85655;
}

.timer__btn--stop:before {
  width: 13px;
  height: 13px;
  border-radius: 2px;
}

.timer-tab__add-btn {
  position: absolute;
  right: -55px;
  top: 50%;
  transform: translateY(-50%);
  width: 41px;
  height: 41px; 
  border-radius: 11px;
  border: 1px solid #dee2e6;
  box-shadow: 0px 2px 6px rgba(48, 48, 49, 0.09);
  font-size: 35px;
  color: #edf4ff;
  background: #4b91fc;
  font-weight: 300;
  cursor: pointer;
}

.timer-tab__add-btn:disabled {
  opacity: 0.3;
}
</style>
