<template>
  <div class="flashcard-generator">
    <h1>AI Flashcard Generator</h1>

    <div class="input-section">
      <input
          v-model="subject"
          placeholder="Enter subject (e.g., Photosynthesis)"
          @keyup.enter="generateFlashcard"
      />
      <button
          @click="generateFlashcard"
          :disabled="isLoading || !engineReady"
      >
        {{ isLoading ? 'Generating...' : 'Generate Flashcard' }}
      </button>
    </div>

    <div v-if="initializationProgress < 100" class="progress">
      Downloading model: {{ initializationProgress.toFixed(0) }}%
      <progress :value="initializationProgress" max="100"></progress>
    </div>

    <div v-if="error" class="error">
      {{ error }}
    </div>

    <div v-if="flashcard.question" class="flashcard">
      <div class="question">Q: {{ flashcard.question }}</div>
      <div class="answer">A: {{ flashcard.answer }}</div>
    </div>
  </div>
</template>

<script setup>
import { CreateMLCEngine } from "@mlc-ai/web-llm";
import { ref, onMounted } from "vue";

// State
const subject = ref("");
const flashcard = ref({ question: "", answer: "" });
const isLoading = ref(false);
const initializationProgress = ref(0);
const engineReady = ref(false);
const error = ref(null);

// Engine instance
let engine;

// Initialize WebLLM
onMounted(async () => {
  try {
    // Check WebGPU support
    if (!navigator.gpu) {
      throw new Error("WebGPU not supported! Please use Chrome 113+ or Edge 113+");
    }

    // Initialize engine with progress tracking
    engine = await CreateMLCEngine(
        "Phi-3-mini-4k-instruct-q4f32_1-MLC", // Lightweight model
        {
          initProgressCallback: (report) => {
            initializationProgress.value = report.progress * 100;
            if (report.progress === 1) {
              engineReady.value = true;
              console.log("Model fully loaded!");
            }
          }
        }
    );

    console.log("Engine initialized successfully");
  } catch (err) {
    console.error("Initialization error:", err);
    error.value = `Initialization failed: ${err.message}`;
  }
});

// Generate flashcard
const generateFlashcard = async () => {
  if (!subject.value.trim() || !engineReady.value) return;

  try {
    isLoading.value = true;
    error.value = null;
    flashcard.value = { question: "", answer: "" };

    const prompt = `Generate exactly one flashcard about "${subject.value}".
    Respond strictly in this format:
    Q: [your question here]
    A: [your answer here]`;

    console.log("Sending prompt:", prompt);

    // Stream the response for better debugging
    const response = await engine.chat.completions.create({
      messages: [{ role: "user", content: prompt }],
      temperature: 0.7,
      max_tokens: 150,
      stream: true
    });

    // Process streaming response
    let fullResponse = "";
    for await (const chunk of response) {
      const content = chunk.choices[0]?.delta?.content || "";
      fullResponse += content;
      console.log("Received chunk:", content);
    }

    console.log("Full response:", fullResponse);

    // Robust parsing of Q&A format
    const qMatch = fullResponse.match(/Q:\s*(.+?)(\n|$)/i);
    const aMatch = fullResponse.match(/A:\s*(.+)/i);

    if (qMatch && aMatch) {
      flashcard.value = {
        question: qMatch[1].trim(),
        answer: aMatch[1].trim()
      };
    } else {
      throw new Error(`Unexpected response format: ${fullResponse}`);
    }

  } catch (err) {
    console.error("Generation error:", err);
    error.value = `Failed to generate: ${err.message}`;
    flashcard.value = {
      question: "Error",
      answer: err.message
    };
  } finally {
    isLoading.value = false;
  }
};
</script>

<style scoped>
.flashcard-generator {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
}

button {
  padding: 10px 15px;
  background-color: #42b983;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:disabled {
  background-color: #cccccc;
  cursor: not-allowed;
}

.progress {
  margin: 15px 0;
}

progress {
  width: 100%;
}

.error {
  color: #ff4444;
  margin: 15px 0;
}

.flashcard {
  margin-top: 20px;
  padding: 15px;
  border: 1px solid #ddd;
  border-radius: 8px;
  background-color: #f9f9f9;
}

.question {
  font-weight: bold;
  margin-bottom: 10px;
}

.answer {
  color: #333;
}
</style>
