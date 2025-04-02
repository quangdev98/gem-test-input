<script lang="ts" setup>
import { ref, computed, watch } from 'vue';

const isPercent = ref<'%' | 'px'>('%');
const internalValue = ref<number>(1.0);

const stepValue = computed(() => (isPercent.value === '%' ? 1 : 1));

const minValue = computed(() => 0);
const maxValue = computed(() => (isPercent.value === '%' ? 100 : Infinity));

const displayValue = computed({
  get: () => internalValue.value.toString(),
  set: (val: string) => {
    const sanitizedValue = val.replace(',', '.').replace(/[^\d.]/g, '');
    const numericValue = parseFloat(sanitizedValue);
    if (!isNaN(numericValue)) {
      internalValue.value = numericValue;
    }
  },
});

watch(isPercent, (newUnit) => {
  if (newUnit === '%' && internalValue.value > 100) {
    internalValue.value = 100;
  }
});


watch(internalValue, (newValue) => {
  if (newValue < minValue.value) {
    internalValue.value = minValue.value;
  } else if (newValue > maxValue.value) {
    internalValue.value = maxValue.value;
  }
});

const decrement = () => {
  if (internalValue.value > minValue.value) {
    internalValue.value -= stepValue.value;
    focusInput();
  }
};

const increment = () => {
  if (internalValue.value < maxValue.value) {
    internalValue.value += stepValue.value;
    focusInput();
  }
};

const onInput = (event: Event) => {
  displayValue.value = (event.target as HTMLInputElement).value;
};

const onBlur = () => {
  if (internalValue.value < minValue.value) {
    internalValue.value = minValue.value;
  }
  if (internalValue.value > maxValue.value) {
    internalValue.value = maxValue.value;
  }
};

const valueInput = ref<HTMLInputElement | null>(null);
const focusInput = () => {
  valueInput.value?.focus();
};

</script>

<template>
  <div
    className="w-screen h-screen bg-neutral-950 flex items-center justify-center text-neutral-100"
  >
    <div className="bg-neutral-800 p-4 rounded-lg wrap-box">
      <div className="box-control">
        <div className="box-control-item">
          <label className="label" for="unit">Unit</label>
          <div className="switch">
            <input
                type="checkbox"
                id="unit"
                v-model="isPercent"
                :true-value="'%'"
                :false-value="'px'"
            />
            <span class="selection"></span>
            <label for="unit">%</label>
            <label for="unit">px</label>
          </div>
        </div>
        <div class="box-control-item">
          <label class="label" for="value">Value</label>
          <div class="input-number">
            <button type="button" @click="decrement" :disabled="internalValue <= minValue">−</button>
            <input
                type="text"
                id="value"
                v-model="displayValue"
                @input="onInput"
                @blur="onBlur"
                ref="valueInput"
            />
            <button type="button" @click="increment" :disabled="internalValue >= maxValue">+</button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<style scoped>
.wrap-box{
  width: 600px;
}
.wrap-box .box-control{
  display: inline-flex;
  flex-direction: column;
  gap: 16px;
}
.wrap-box .box-control .box-control-item{
  display: inline-flex;
  align-items: center;
  gap: 8px;
}
.wrap-box .box-control .box-control-item .label{
  width: 100px;
  font-weight: 400;
  font-size: 12px;
  line-height: calc(20 / 12);
  letter-spacing: 0;
  color: #AAAAAA;
}
.switch{
  position: relative;
  height: 36px;
  width: 140px;
  background: #212121;
  border-radius: 4px;
  border: 2px solid transparent;
}
.switch input{ display:none; }
.switch label{
  position: relative;
  float:left;
  font-size:14px;
  width:50%;
  height:32px;
  text-align:center;
  z-index: 2;
  cursor:pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #AAAAAA;
}
.selection{
  position:absolute;
  display: block;
  top:0;
  left:0;
  width:50%;
  background:#424242;
  height:32px;
  border-radius:4px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
  z-index: 1;
  -webkit-transition: 0.15s ease-out;
  -moz-transition: 0.15s ease-out;
  -o-transition: 0.15s ease-out;
  transition: 0.15s ease-out;
}

.switch input[type=checkbox]:checked + .selection{
  left:50%;
}
.box-control-item {
  margin-bottom: 1rem;
}
.label {
  display: block;
  margin-bottom: 0.5rem;
}
.input-number {
  display: flex;
  align-items: center;
  background-color: #212121;
  border-radius: 4px;
  height: 36px;
  width: 140px;
  overflow: hidden;
}
.input-number button {
  width: 36px;
  height: 36px;
  font-size: 18px;
  cursor: pointer;
  transition: all .3s;
}
.input-number button:hover,
.input-number input:hover{
  background-color: #3B3B3B;
}
.input-number input[type="text"] {
  width: 68px;
  text-align: center;
  font-size: 16px;
  height: 36px;
}
.input-number input[type="text"]:focus-visible{
  box-shadow: none;
}

.input-number button:disabled{
  color: #AAAAAA;
}
</style>
