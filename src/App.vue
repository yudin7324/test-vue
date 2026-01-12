<script>
import { ref, onMounted } from 'vue';

export default {
  setup() {
    const userItems = ref([]);
    const choiceItems = ref([]);
    const selectedUserItems = ref([]);
    const selectedChoiceItem = ref(null);

    const loadData = async () => {
      const userResponse = await fetch('/src/data/userItems.json');
      userItems.value = await userResponse.json();

      const choiceResponse = await fetch('/src/data/choiceItems.json');
      choiceItems.value = await choiceResponse.json();
    };

    const toggleUserItem = (item) => {
      if (selectedUserItems.value.includes(item)) {
        selectedUserItems.value = selectedUserItems.value.filter(selectedItem => selectedItem.id !== item.id);
      } else {
        if (selectedUserItems.value.length < 6) {
          selectedUserItems.value.push(item);
        } else {
          alert("Можно выбрать не более 6 товаров");
        }
      }
    };

    const selectChoiceItem = (item) => {
      selectedChoiceItem.value = item;
    };

    const isUserItemSelected = (item) => {
      return selectedUserItems.value.some(selectedItem => selectedItem.id === item.id);
    };

    const isChoiceItemSelected = (item) => {
      return selectedChoiceItem.value && selectedChoiceItem.value.id === item.id;
    };


    onMounted(loadData);

    return {
      userItems,
      choiceItems,
      selectedUserItems,
      selectedChoiceItem,
      toggleUserItem,
      selectChoiceItem,
      isUserItemSelected,
      isChoiceItemSelected
    };
  }
};
</script>

<template>
  <div class="block">

    <div class="block__row">
      <div class="block__content">
        <div class="block__content-items" v-for="item in selectedUserItems" :key="item.id">{{ item.name }}</div>
      </div>

      <div class="block__content">
        <div class="block__content-items" v-if="selectedChoiceItem">{{ selectedChoiceItem.name }}</div>
      </div>
    </div>

    <div class="block__row">
      <div class="block__content">
        <div 
          v-for="item in userItems" 
          :key="item.id" 
          :class="['block__item', { selected: isUserItemSelected(item) }]"
          @click="toggleUserItem(item)"
        >
          {{ item.name }}
        </div>
      </div>

      <div class="block__content">
        <div 
          v-for="item in choiceItems" 
          :key="item.id" 
          :class="['block__item', { selected: isChoiceItemSelected(item) }]"
          @click="selectChoiceItem(item)"
        >
          {{ item.name }}
        </div>
      </div>
    </div>
  </div>
</template>

<style>

.block {
  display: flex;
  flex-direction: column;
  gap: 20px;
  max-width: 1650px;
  margin: 0 auto;
  padding: 80px;
}

.block__row {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
}

.block__content {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  gap: 10px;
  border: 1px solid #000000;
  padding: 10px;
  min-height: 100px;
  flex-grow: 1;
}

.block__content-items {
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid #000000;
}

.block__item {
  padding: 5px;
  margin: 5px 0;
  cursor: pointer;
  border: 1px solid #000000;
}

.block__item.selected {
  background-color: burlywood;
}

</style>
