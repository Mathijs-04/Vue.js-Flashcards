<script setup>
import {CreateMLCEngine} from "@mlc-ai/web-llm";
import {ref, onMounted, watch} from "vue";

const props = defineProps({
  subject: String
});

const emit = defineEmits(['update:flashcard', 'update:loading', 'update:engine-ready']);
const isLoading = ref(false);
const initializationProgress = ref(0);
const engineReady = ref(false);
const error = ref(null);
let engine;

const MODEL_ID = "Mistral-7B-Instruct-v0.2-q4f16_1-MLC";

const initEngine = async () => {
  try {
    if (!navigator.gpu) throw new Error("WebGPU not supported");
    const adapter = await navigator.gpu.requestAdapter();
    if (!adapter) throw new Error("No GPU adapter found");

    engine = await CreateMLCEngine(
        MODEL_ID,
        {
          initProgressCallback: (report) => {
            initializationProgress.value = report.progress * 100;
            if (report.progress === 1) {
              engineReady.value = true;
              emit('update:engine-ready', true);
            }
          },
          gpuAdapter: adapter,
          config: {temperature: 0.3, max_seq_len: 1024}
        }
    );
  } catch (err) {
    error.value = `Initialization failed: ${err.message}`;
  }
};

const generateFlashcard = async (topic) => {
  if (!topic?.trim() || !engineReady.value) return;

  isLoading.value = true;
  emit('update:loading', true);
  error.value = null;

  try {
    const prompt = `Create ONE flashcard about "${topic}" with this EXACT format:
Q: [question testing conceptual understanding]
A: [concise answer with key points, max 25 words]`

    const response = await engine.chat.completions.create({
      messages: [{role: "user", content: prompt}],
      temperature: 0.3,
      max_tokens: 150
    });

    const content = response.choices[0]?.message?.content || "";
    const qaMatch = content.match(/Q:\s*(.+?)\s*A:\s*(.+)/is);

    if (!qaMatch) throw new Error("Invalid format");

    const question = qaMatch[1].trim();
    let answer = qaMatch[2].trim();

    answer = answer
        .replace(/\s+/g, ' ')
        .replace(/, etc\.?/i, '')
        .replace(/\.$/, '');

    if (answer.split(' ').length > 25) {
      answer = answer.split(', ')
          .filter((_, i) => i < 3)
          .join(', ');
    }

    emit('update:flashcard', {question, answer});

  } catch (err) {
    error.value = "Couldn't generate flashcard";
    emit('update:flashcard', {
      question: topic,
      answer: "Please try again with a different topic"
    });
  } finally {
    isLoading.value = false;
    emit('update:loading', false);
    emit('update:loading', false);
  }
};

onMounted(initEngine);
watch(() => props.subject, generateFlashcard);
</script>

<template>
  <div>
    <div v-if="initializationProgress < 100">
      Loading model... {{ initializationProgress.toFixed(0) }}%
      <progress :value="initializationProgress" max="100"></progress>
    </div>
    <div v-if="error" class="error">
      {{ error }}
    </div>
  </div>
</template>

<style scoped>
.error {
  color: #ff4444;
  margin: 1rem 0;
  padding: 0.75rem;
  border: 1px solid #ffcccc;
  border-radius: 6px;
  background: #fff0f0;
}

progress {
  width: 100%;
  height: 8px;
  margin: 0.5rem 0;
  background-color: #35495e;
}
</style>
