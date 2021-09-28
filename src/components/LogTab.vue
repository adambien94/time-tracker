<template>
  <div class="log-tab tab">
    <h3 class="log-tab__title">Title 🐱‍👤</h3>
    <button class="log-tab__delete-btn" @click="$emit('deleteLog', id)"/>
    <div class="log-tab__content">
      <span class="log-tab__activity">{{ activity }}</span>
      <span class="log-tab__category" v-if="shouldCategoryBeDisplayed"> - {{ category }}</span>
      <button 
        v-if="mode === 'read-mode'"
        class="log-tab__edit-btn" 
        @click="mode = 'edit-mode'" 
      >
        edit
      </button>
      <button 
        v-else
        class="log-tab__edit-btn" 
        @click="onSaveBtnClick" 
      >
        save
      </button>
      <span class="log-tab__time">{{ time }}</span>
      <p class="log-tab__description">Lorem ipsum dolor, sit amet consectetur adipisicing elit. Laudantium quasi at, nostrum incidunt voluptatem.</p>
    </div>
    <div class="log-tab__content" v-if="mode === 'edit-mode'">
      <input 
        type="text" 
        placeholder="Activity" 
        v-model="editedActivity"
      >
      <span> - </span>
      <input 
        type="text" 
        placeholder="Category" 
        v-model="editedCategory"
      >
      <input 
        class="log-tab__time" 
        placeholder="00:00:00" 
        v-model="editedTime"
      >
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, computed  } from 'vue';

export default defineComponent({
  name: 'log-tab',
  props: {
    id: Number,
    activity: String,
    category: String,
    time: String,
    date: String
  },
  setup(props, context) {
    const mode = ref<string>('read-mode');
    const editedActivity = ref<string>('');
    const editedCategory = ref<string>('');
    const editedTime = ref<string>('');

    const shouldCategoryBeDisplayed = computed(() => !!props.category?.trim())

    const updateLog = (): void => {
      context.emit('updateLog', {
        id: props.id,
        activity: editedActivity.value || props.activity,
        category: editedCategory.value || props.category,
        time: editedTime.value || props.time,
        date: props.date
      });
    }

    const closeEditMode = (): void => {
      mode.value = 'read-mode';
      editedActivity.value= '';
      editedCategory.value= '';
      editedTime.value= '';
    }

    const onSaveBtnClick = (): void => {
      updateLog();
      closeEditMode();
    }

    return {
      mode,
      editedActivity,
      editedCategory,
      editedTime,
      shouldCategoryBeDisplayed,
      updateLog,
      closeEditMode,
      onSaveBtnClick
    }
  }
});
</script>

<style>
.log-tab {
  position: relative;
}

.log-tab__delete-btn {
  position: relative;
  background: transparent;
  position: absolute;
  border: none;
  right: 21px;
  top: 13px;
  width: 23px;
  height: 23px;
  cursor: pointer;
}

.log-tab__delete-btn:after {
  content: '+';
  font-weight: 100;
  font-size: 33px;
  color: #e85655;
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%) rotate(45deg);
}

.log-tab__content {
  margin-top: 10px;
  padding: 10px 25px;
  position: relative;
}

.log-tab__activity,
.log-tab__category {
  font-size: 15px;
  text-transform: capitalize;
}

.log-tab__activity,
.log-tab__time {
  font-weight: 500;
}

.log-tab__time {
 position: absolute;
 right: 25px;
 top: 50%;
 transform: translateY(-50%);
 color: #78808a;
 font-size: 17px;
}

input.log-tab__time{
  width: 117px;
  font-weight: 600;
}

.log-tab__description {
  width: 60%;
  font-size: 13px;
  margin-top: 5px;
  color: #78808a;
  line-height: 19px;
}

.log-tab__edit-btn {
  margin: 0 10px;
  border: none;
  background: none;
  color: #4b91fc;
  cursor: pointer;
}
</style>
