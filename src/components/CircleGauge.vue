<template>
 <div :style="containerStyle" class="gauge__component">
    <div>
      <div class="gauge__ring" :style="getGaugeStyle">
        <div class="gauge__inner">
          <div class="pin"></div>
          <div class="gauge__value gauge__value--current">64.91</div>
          
          <div class="gauge__value gauge__value--start">0</div>
          <div class="gauge__value gauge__value--middle">50.00</div>
          <div class="gauge__value gauge__value--end">100.00</div>
        </div>
      </div>
    </div>
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
  --pointerdeg: -45deg;
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
  border-bottom: 13vmin solid black;
  top: -2vmin;
  left: 50%;
  transform: translateX(-50%) rotate(0deg);
}

.gauge__value {
  position: absolute;
  color: black;
  font-size: 2vmin; 
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
  