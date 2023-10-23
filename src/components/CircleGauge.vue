<template>
  <div :style="containerStyle" class="gauge__component">
    <div>
      <div class="gauge__ring" :style="getGaugeStyle"> 
        <div class="gauge__inner">
          <div class="gauge__value gauge__value--current">
            {{ currentValue }}
          </div>
          <div class="gauge__value gauge__value--start">0</div>
          <div class="gauge__value gauge__value--middle">50.00</div>
          <div class="gauge__value gauge__value--end">100.00</div>
        </div>
      </div>
    </div>
    <div
      class="pin"
      @mousedown="startDrag"
      @mousemove="rotatePin"
      @mouseup="stopDrag"
      :style="pinStyle"
    ></div>
  </div>
</template>
  
  <script lang="ts">
import { defineComponent, PropType } from "vue";

interface IntervalItem {
  value: number;
  color: string;
}

export default defineComponent({
  props: {
    intervals: {
      type: Array as PropType<IntervalItem[]>,
      required: true,
    },
    size: {
      type: Number,
      default: 100,
    },
  },
  data() {
    return {
      isDragging: false,
      startX: 0,
      startY: 0,
      currentAngle: 58.6, // Initial angle 
    };
  },
  computed: {
    containerStyle() {
      return {
        width: this.size + "px",
        height: this.size + "px",
      };
    },
    getGaugeStyle() {
      const gradient = this.intervals
        .map(
          (interval, index) =>
            `${interval.color} ${
              90 + (index / (this.intervals.length - 1)) * 150
            }deg`
        )
        .join(", ");

      const finalGradient = `${gradient}, transparent 0deg `;
      return {
        background: `conic-gradient(from ${240}deg at 50% 50%, ${finalGradient})`,
      };
    },
    pinStyle() {
        // Rotate the pin based on 'currentAngle' 
      return {
        transform: `rotate(${this.currentAngle}deg)`,
        transformOrigin: "50% 50%", // Around the center of the pin
      };
    },
    currentValue() {
      // Calculate the value based on the pin's angle
      const minAngle = 0;
      const maxAngle = 360;
      const minValue = -23.33; // To set initial value to 0.00
      const maxValue = 120;
      const angle = this.currentAngle;

      return (
        ((angle - minAngle) / (maxAngle - minAngle)) * (maxValue - minValue) +
        minValue
      ).toFixed(2);
    },
  },
  methods: {
    startDrag(event: any) {
      this.isDragging = true;
      this.startX = event.clientX;
      this.startY = event.clientY;
    },
    rotatePin(event: any) {
  if (this.isDragging) {
    const deltaX = event.clientX - this.startX;
    const deltaY = event.clientY - this.startY;

    let angle = Math.atan2(deltaY, deltaX) * (180 / Math.PI);

    // To Ensure the angle is within the non-transparent range
    const nonTransparentRange = [60, 240];
    const percentage = (angle - nonTransparentRange[0]) / (nonTransparentRange[1] - nonTransparentRange[0]);

    // Restrict rotation within the non-transparent range
    if (percentage < 0 || percentage > 1) {
      return; 
    }

    this.currentAngle = angle;
    this.startX = event.clientX;
    this.startY = event.clientY;
  }
},

    stopDrag() {
      this.isDragging = false;
    },
  },
});
</script>
  
  <style scoped>
.gauge__component {
  display: flex;
  justify-content: center;
  align-items: center;
}
.gauge__ring {
  position: relative;
  --pointerleft: 11%;
  --pointertop: 11%;
  --pointerdeg: 0deg;
  width: 50vmin;
  height: 50vmin;
  border-radius: 50%;
}
.gauge__inner {
  width: 75%;
  height: 75%;
  background: #ffffff;
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  border-radius: 50%;
}
.gauge__value--current {
  display: inline-block;
  top: 50%;
  left: 50%;
  transform: translateX(-50%) translateY(-50%);
  text-align: center;
  font-size: 6vmin !important;
}
.pin {
  position: absolute;
  content: "";
  left: var(--pointerleft);
  top: var(--pointertop);
  transform: rotate(var(--pointerdeg));
}
.pin::before {
  content: "";
  position: absolute;
  width: 0;
  height: 0;
  border-left: 1vmin solid transparent;
  border-right: 1vmin solid transparent;
  border-top: 15vmin solid transparent;
  border-bottom: 10vmin solid black;
  top: 0vmin;
  left: 50%;
  transform: translateX(-50%) rotate(0deg);
}
.gauge__value {
  position: absolute;
  color: black;
  font-size: 3vmin;
}
.gauge__value--start {
  left: -20%;
  bottom: 2%;
}
.gauge__value--middle {
  top: -30%;
  left: 50%;
  transform: translateX(-50%);
}
.gauge__value--end {
  right: -20%;
  bottom: 2%;
}
</style>
  