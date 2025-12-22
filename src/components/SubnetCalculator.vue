<template>
  <div>
    <h2>Калькулятор подсетей</h2>
    
    <div class="inputs">
      <input
        v-model="ip"
        placeholder="IP (192.168.1.150)"
        :class="{ error: !valid && ip }"
        @keyup.enter="calculate"
      />
      
      <select v-model="mask">
        <option v-for="opt in options" :value="opt.value" :key="opt.value">
          {{ opt.label }}
        </option>
      </select>
      
      <button @click="calculate" :disabled="!valid">
        Рассчитать
      </button>
    </div>
    
    <div v-if="result" class="result">
      <h3>Результат:</h3>
      <p>IP: {{ result.ip }}</p>
      <p>Маска: {{ result.mask }}</p>
      <p>Сеть: {{ result.network }}</p>
      <p>Адресов: {{ result.addresses }}</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from 'vue';
import { MASK_OPTIONS } from '../constants/maskOptions';
import { isIpValid } from '../utils/ipValidation';
import { getNetworkAdress, getAddressesCount } from '../utils/networkCalculations';

// Тип для результатов
interface ResultType {
  ip: string;
  mask: string;
  network: string;
  addresses: number;
}

const ip = ref('192.168.1.150');
const mask = ref('24/255.255.255.0');
// Указываем тип результата - может быть ResultType или null
const result = ref<ResultType | null>(null);

const options = MASK_OPTIONS;
const valid = computed(() => isIpValid(ip.value));

function calculate() {
  if (!valid.value) return;
  
  const maskValue = mask.value.split('/')[1];
  
  // Создаем объект с правильным типом
  result.value = {
    ip: ip.value,
    mask: mask.value,
    network: getNetworkAdress(ip.value, maskValue),
    addresses: getAddressesCount(maskValue)
  };
}
</script>

<style scoped>
div {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
}

h2 {
  margin-bottom: 20px;
  text-align: center;
}

.inputs {
  display: flex;
  flex-direction: column;
  gap: 10px;
  margin-bottom: 20px;
}

input, select, button {
  padding: 10px;
  font-size: 16px;
}

input.error {
  border-color: red;
}

button {
  background: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}

button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

.result {
  background: #f5f5f5;
  padding: 15px;
  border-radius: 5px;
}

.result p {
  margin: 5px 0;
}
</style>