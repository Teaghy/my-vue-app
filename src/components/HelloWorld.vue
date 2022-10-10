<template>
  <div class="move-container" @click.self="clearAll">
    <div
      :class="[
        'comp-item',
        compIndex.includes('1') ? 'select-item' : 'not-select-item',
      ]"
      :style="{
        top: 345 + 'px',
        left: 345 + 'px'
      }"
      @click="handleClick({ id: '1', left: 345, top: 345, rotate: 0 },$event)"
    >
      1
    </div>
    <div
      :class="[
        'comp-item',
        compIndex.includes('2') ? 'select-item' : 'not-select-item',
      ]"
      :style="{
        top: 100 + 'px',
        left: 1200 + 'px'
      }"
      @click="handleClick({ id: '2', left: 1200, top: 100, rotate: 0 },$event)"
    >
      2
    </div>
    <div
      :class="[
        'comp-item',
        compIndex.includes('3') ? 'select-item' : 'not-select-item',
      ]"
      :style="{
        top: 345 + 'px',
        left: 800 + 'px'
      }"
      @click="handleClick({ id: '3', left: 800, top: 345, rotate: 0 }, $event)"
    >
      3
    </div>
  </div>
</template>

<script setup>
import Moveable  from 'moveable';
import { ref, nextTick, onMounted, watch } from 'vue';


defineProps({
  msg: String
})
let moveable;
function init() {
  moveable = new Moveable(document.body, {
    target: '.select-item',
    // If the container is null, the position is fixed. (default: parentElement(document.body))
    // container: document.body,
    elementGuidelines: [].slice.call(
      document.querySelectorAll('.comp-item'),
    ),
    // individualGroupable: true,
    draggable: true,
    resizable: true,
    scalable: true,
    rotatable: true,
    warpable: true,
    // Enabling pinchable lets you use events that
    // can be used in draggable, resizable, scalable, and rotateable.
    // pinchable: true, // ["resizable", "scalable", "rotatable"]
    origin: false,
    keepRatio: false,
    // Resize, Scale Events at edges.
    edge: false,
    throttleDrag: 0,
    throttleResize: 0,
    // throttleScale: 0,
    zoom: 1,
    throttleRotate: 0,
    snappable: true,
    elementSnapDirections: true,
    // verticalGuidelines: [0, 200, 400],
    // horizontalGuidelines: [0, 200, 400],
    snapThreshold: 5,
    renderDirections: ['nw', 'n', 'ne', 'w', 'e', 'sw', 's', 'se'],
  });
  moveable
    .on('drag', (e) => {
      console.log(e);
      e.target.style.transform = e.transform;
    })
    .on('dragEnd', ({ target, isDrag, clientX, clientY }) => {
      console.log('onDragEnd', target, isDrag);
    })
}

onMounted(() => {
  init()
});

const compIndex = ref([]);
const handleClick = (comp, e) => {
  const isMultiple = e.ctrlKey;
  console.log(isMultiple, moveable);
  if (isMultiple) {
    moveable.rotatable = false;
    const p = compIndex.value.indexOf(comp.id);
    if (p !== -1) {
      compIndex.value.splice(p, 1);
    } else {
      compIndex.value.push(comp.id);
    }
  } else {
    moveable.rotatable = true;
    compIndex.value = [comp.id];
  }
}

function clearAll() {
  compIndex.value = [];
}

watch(() => compIndex, (n) => {
  // moveable.renderDirections = false;
  nextTick(() => {
    moveable.updateSelectors();
    debugger
    moveable.elementGuidelines = [].slice.call(
        document.querySelectorAll('.not-select-item'),
      );
  });
}, {
  deep: true
})
</script>

<style scoped>
.read-the-docs {
  color: #888;
}
.move-container {
  height: 100%;
  width: 100%;
}
.move-container {
  position: relative;
  height: 100%;
  width: 100%;
  background-color: black;
}
.comp-item {
  position: absolute;
  height: 300px;
  width: 300px;
  background: red;
  border: 1px solid transparent;
}
.select-item {
  border-color: red;
}
</style>
