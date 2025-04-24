<template>
  <div class="item">
    <p class="item__text">
      {{ item.text }}
    </p>

    <svg
      xmlns="http://www.w3.org/2000/svg"
      viewBox="0 0 24 24"
      class="item__icon"
      ref="my-item"
    >
      <path d="M7.41,8.58L12,13.17L16.59,8.58L18,10L12,16L6,10L7.41,8.58Z" />
    </svg>

    <div class="item__content">
      <slot>{{ item.body }}</slot>
    </div>
  </div>
</template>

<script setup>
import { useTemplateRef } from "vue";
const myItem = useTemplateRef("my-item");
const props = defineProps({
  item: { type: [Array, Object, String], required: true }, // значения для каждого Item
  windowHeight: { type: Number, required: true }, // значения окна
});

document.onscroll = () => {
  console.log(myItem.value.getBoundingClientRect().top, props.windowHeight / 2);

  if (myItem.value.getBoundingClientRect().top > props.windowHeight / 2)
    myItem.value.style.backgroundColor = "red";
  else {
    myItem.value.style.backgroundColor = "white";
  }
};
</script>

<style>
.item {
  width: 100%;
  position: relative;
  display: grid;
  align-items: center;
  grid-template-columns: 1fr 40px 1fr;
  gap: 35px;
}
.item::before {
  height: 50%;
  content: "";
  position: absolute;
  left: 50%;
  top: 0%;
  border-left: 3px solid var(--white);
}
.item::after {
  height: calc(50% + 70px);
  content: "";
  position: absolute;
  left: 50%;
  top: calc(50% + 20px);
  border-left: 3px solid var(--white);
}

.item__text {
  text-align: start;
  order: 1;
  font-size: 1.3rem;
  font-weight: bold;
}
.item:nth-child(2n) .item__text {
  order: -1;
  text-align: end;
}

.item__content {
  border: 1px solid var(--white);
  border-radius: 0.625rem;
  box-shadow: 0 10px 15px -3px var(--color), 0 4px 6px -4px var(--color);
  padding: 25px;
  background-color: var(--color);
  position: relative;
  order: -1;
}
.item:nth-child(2n) .item__content {
  order: 1;
}

.item__content:before {
  position: absolute;
  content: "";
  border: solid transparent;
  left: 100%;
  top: 50%;
  border-left-color: var(--white);
  border-width: 12px;
  margin-top: -12px;
}
.item:nth-child(2n) .item__content:before {
  content: "";
  border: solid transparent;
  position: absolute;
  right: 100%;
  left: -100%;
  top: 50%;
  border-right-color: var(--white);
  border-width: 12px;
  margin-top: -12px;
}

.item__icon {
  height: auto;
  width: auto;
  left: 1px;
  background-color: var(--white);
  border-radius: 50%;
  position: relative;
}
</style>