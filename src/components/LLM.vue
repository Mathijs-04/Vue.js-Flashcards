<script setup>
import { CreateMLCEngine } from "@mlc-ai/web-llm";
import { ref, onMounted, watch } from "vue";

const props = defineProps({
  subject: String
});

const emit = defineEmits(['update:flashcard', 'update:loading']);
const isLoading = ref(false);
const initializationProgress = ref(0);
const engineReady = ref(false);
const error = ref(null);
let engine;

const initEngine = async () => {
  try {
    if (!navigator.gpu) {
      throw new Error("WebGPU not supported");
    }

    const adapter = await navigator.gpu.requestAdapter();
    if (!adapter) throw new Error("No GPU adapter found");

    console.log("Using GPU:", adapter.info.vendor, adapter.info.architecture);

    engine = await CreateMLCEngine(
        "TinyLlama-1.1B-Chat-v1.0-q4f32_1-MLC",
        {
          initProgressCallback: (report) => {
            initializationProgress.value = report.progress * 100;
            if (report.progress === 1) engineReady.value = true;
          },
          gpuAdapter: adapter
        }
    );
  } catch (err) {
    error.value = `Initialization error: ${err.message}`;
    console.error("Engine init failed:", err);
  }
};

const generateFlashcard = async (topic) => {
  if (!topic?.trim() || !engineReady.value) return;

  try {
    isLoading.value = true;
    emit('update:loading', true);
    error.value = null;

    const prompt = `Create exactly one flashcard about ${topic}.
    Format strictly as:
    Q: [question]
    A: [answer]
    No extra text or numbering.`;

    const response = await engine.chat.completions.create({
      messages: [{ role: "user", content: prompt }],
      temperature: 0.1,
      max_tokens: 100,
      stop: ["\n\n"]
    });

    const content = response.choices[0]?.message?.content || "";
    const [qLine, aLine] = content.split('\n').filter(l => l.trim());

    const question = qLine?.replace(/^Q:\s*/i, '').trim();
    const answer = aLine?.replace(/^A:\s*/i, '').trim();

    if (question && answer) {
      emit('update:flashcard', { question, answer });
    } else {
      throw new Error("Invalid response format");
    }
  } catch (err) {
    error.value = "Failed to generate flashcard";
    emit('update:flashcard', {
      question: "Error",
      answer: "Please try a different topic"
    });
  } finally {
    isLoading.value = false;
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
    <div v-if="error" class="error">{{ error }}</div>
  </div>
</template>

<style scoped>

</style>
