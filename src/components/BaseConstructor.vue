<template>
  <div class="constructor">
    <div v-for="(item, index) in data" :key="index" class="item">
      <p class="item__text">
        {{ item.text }}
      </p>
      <div class="item__scroll">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          ref="icon"
          fill="black"
          class="icon"
        >
          <path
            d="M7.41,8.58L12,13.17L16.59,8.58L18,10L12,16L6,10L7.41,8.58Z"
          />
        </svg>
        <div ref="scroll" class="scroll"></div>
      </div>

      <div class="item__content">
        <slot>{{ item.body }}</slot>
      </div>
    </div>
  </div>
</template>
  
  <script setup>
import { useTemplateRef } from "vue";
const icons = useTemplateRef("icon");
const scrolls = useTemplateRef("scroll");

const props = defineProps({
  data: { type: [Array, Object, String], required: true }, // значения которые добавляем по умолчанию
});
const viewportHalfHeight = window.innerHeight / 2;
document.onscroll = () => {
  for (let icon of icons.value) {
    const first = icons.value.at(0).getBoundingClientRect();
    const current = icon.getBoundingClientRect();
    if (current.top <= viewportHalfHeight) {
      icon.style.backgroundColor = "var(--scroll)";
      icon.setAttribute("fill", "var(--white)");
      icon.parentNode.previousSibling.style.color = "var(--scroll)";
      const firstItem =
        icons.value.at(0).getBoundingClientRect().top + window.scrollY;
      const lastItem =
        icons.value.at(-1).getBoundingClientRect().top + window.scrollY;
      const currentItem = current.top + window.scrollY;

      if (currentItem + 10 > lastItem) {
        scrolls.value.at(0).style.height = lastItem - firstItem + "px";
      } else {
        const diferent =
        viewportHalfHeight - icons.value[0].getBoundingClientRect().bottom;
        scrolls.value[0].style.height = (diferent > 0 ? diferent : 0) + "px";
      }
      // let diferent =
      // viewportHalfHeight - icons.value[0].getBoundingClientRect().bottom;

      // console.log("l  d", current, last);

      // if (currentItem.bottom + 10 > lastItem) {
      //   diferent = icons.value.at(-1).getBoundingClientRect().bottom;
      // }
      // scrolls.value[0].style.height = currentItem.y + "px";
    } else {
      icon.style.backgroundColor = "var(--white)";
      icon.parentNode.previousSibling.style.color = "var(--black)";
      icon.setAttribute("fill", "var(--black)");
    }
  }

  let observer = new IntersectionObserver(
    (entries) => {
      entries.forEach((entry) => {
        if (entry.isIntersecting) {
          entry.target.classList.add("active");
        }
      });
    },
    {
      threshold: 0.1,
    }
  );

  const contents = document.querySelectorAll(".item__content");
  contents.forEach((content) => {
    observer.observe(content);
  });
};
</script>
  
<style scoped>
.constructor {
  display: flex;
  flex-direction: column;
  gap: 70px;
  overflow: hidden;
  padding: 40px;
}

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
.item:first-of-type::before {
  border: none;
}
.item:last-of-type::after {
  border: none;
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
  box-shadow: 0 10px 15px -3px var(--bgColor), 0 4px 6px -4px var(--bgColor);
  padding: 25px;
  background-color: var(--bgColor);
  position: relative;
  order: -1;
  transform: translateY(100px);
  opacity: 0;
  transition: transform 0.8s, opacity 0.5s;
}
.active {
  transform: translateY(0px);
  opacity: 1;
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
  border-left-color: var(--bgColor);
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
  border-right-color: var(--bgColor);
  border-width: 12px;
  margin-top: -12px;
}
.item__scroll {
  display: flex;
  flex-direction: column;
  align-items: center;
  position: relative;
}
.scroll {
  width: 3px;
  height: 0px;
  left: 50%;
  top: 100%;
  position: absolute;
  background-color: var(--scroll);
  z-index: 1;
}
.icon {
  left: 1px;
  background-color: var(--white);
  border-radius: 50%;
  position: relative;
  z-index: 2;
}
</style>