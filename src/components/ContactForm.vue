<script setup lang="ts">
import { ref } from 'vue'
import CustomSelect from '@/components/CustomSelect.vue'

const formData = ref({
  budget: null as string | null,
  timeline: '',
  howFoundUs: '',
  favoriteMovie: '',
})

const formFields = {
  timeline: {
    label: 'TIMELINE EXPECTATION',
    placeholder: 'TYPE HERE',
    required: true,
  },
  howFound: {
    label: 'HOW DID YOU FIND US?',
    placeholder: 'TYPE HERE',
    required: true,
  },
  favorite: {
    label: 'FAVORITE MOVIE OR ALBUM',
    placeholder: 'TYPE HERE',
    required: false,
  },
  budget: {
    label: 'BUDGET EXPECTATION',
    placeholder: 'SELECT',
    required: true,
    options: [
      { value: 'less40k', label: 'LESS THAN 40K' },
      { value: 'more40k', label: 'MORE THAN 40K' },
      { value: 'more100k', label: 'MORE THAN 100K' },
      { value: 'more200k', label: 'MORE THAN 200K' },
    ],
  },
}

const handleSubmit = async () => {
  console.log('ContactForm submitting:', formData.value)
}
</script>

<template>
  <div class="contact-form">
    <form @submit.prevent="handleSubmit" class="form">
      <div class="form-row">
        <div class="form-group">
          <CustomSelect
            v-model="formData.budget"
            :options="formFields.budget.options"
            :label="formFields.budget.label"
            :placeholder="formFields.budget.placeholder"
            :required="formFields.budget.required"
          />
        </div>
        <div class="form-group">
          <label for="timeline" class="font-mono"
            >{{ formFields.timeline.label }}<span class="required">*</span></label
          >
          <input
            class="font-sans"
            id="timeline"
            :class="{ filled: !!formData.timeline }"
            v-model="formData.timeline"
            type="text"
            :required="formFields.timeline.required"
            :placeholder="formFields.timeline.placeholder"
          />
        </div>
      </div>

      <div class="form-row">
        <div class="form-group">
          <label for="howFound" class="font-mono"
            >{{ formFields.howFound.label }}<span class="required">*</span></label
          >
          <input
            class="font-sans"
            id="howFound"
            v-model="formData.howFoundUs"
            :class="{ filled: !!formData.howFoundUs }"
            type="text"
            :required="formFields.howFound.required"
            :placeholder="formFields.howFound.placeholder"
          />
        </div>
        <div class="form-group">
          <label for="favorite" class="font-mono">{{ formFields.favorite.label }}</label>
          <input
            class="font-sans"
            id="favorite"
            :class="{ filled: !!formData.favoriteMovie }"
            v-model="formData.favoriteMovie"
            type="text"
            :required="formFields.favorite.required"
            :placeholder="formFields.favorite.placeholder"
          />
        </div>
      </div>

      <button type="submit" class="submit-btn font-sans">LFG</button>
    </form>
  </div>
</template>

<style scoped>
.contact-form {
  height: 100%;
  display: flex;
  align-items: center;
  width: 100%;
}

.form {
  display: flex;
  flex-direction: column;
  gap: 24px;
  width: 100%;
}

.form-row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-group label {
  font-weight: 400;
  font-size: 12px;
  color: #909090;
  text-transform: uppercase;
  line-height: 125%;
}

.form-group input {
  padding: 16px;
  border: 1px solid #fffef4;
  background-color: black;
  color: #fffef4;
  border-radius: 8px;
  transition: border-color 0.2s ease-in-out;
  outline: none;
  font-weight: 700;
  font-size: 16px;
  outline: none;
  text-transform: uppercase;
}

.form-group input::placeholder {
  color: #909090;
  font-size: inherit;
  font-family: inherit;
  font-weight: 700;
  text-transform: uppercase;
}

.form-group input.filled {
  border-color: #00ff6a;
}

.submit-btn {
  padding: 15px;
  text-transform: uppercase;
  font-weight: 700;
  border-radius: 8px;
  border: 1px solid #00ff6a;
  background-color: transparent;
  transition:
    background-color 0.2s ease-in-out,
    color 0.2s ease-in-out;
  color: #00ff6a;
  font-size: 16px;
  cursor: pointer;
}

.submit-btn:hover {
  background-color: #00ff6a;
  color: #000000;
}

@media (max-width: 768px) {
  .form-row {
    grid-template-columns: 1fr;
    gap: 24px;
  }
}
</style>
