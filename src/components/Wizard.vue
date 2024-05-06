<script setup>
import { computed, ref } from 'vue'

// Variables
const ERRORS_MSG = ['Invalid Username.', 'Invalid email address.']
const Headers = ref(['Step: username', 'Step: email', 'Step: review'])
const Current_Step = ref(0)
const Model = ref({
  username: '',
  email: '',
})
const Data_Structure = ref([{
  value: Model.value.username,
  id: 'username',
  name: 'username',
  label: 'Username:',
  placeholder: 'enter your username',
  regex: /^(?!.*[@\s!#$%^&*()\-+=\[\]{};:'"\\|,.<>\/?])[\s\S]{5,20}$/,
  error: '',
}, {
  value: Model.value.email,
  id: 'email',
  name: 'email',
  label: 'Email:',
  placeholder: 'enter your email',
  regex: /^(?!\s*$)(?:[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,})$/,
  error: '',
}])

// Computed
const isFieldInvalid = computed(() => {
  /**
   * Based on my understanding of the tests, I Defined the regex pattern for validation
   * @description for username regex
   * ^: Start of the string.
   * (?!.*[@\s!#$%^&*()\-+=\[\]{};:'"\\|,.<>\/?]): Negative lookahead to ensure the string does not contain @, space, or any symbol except _.
   * .{5,20}: Matches any character (except newline) between 5 and 20 times.
   * $: End of the string.
   * @description for email regex
   * ^(?!\s*$): Negative lookahead to ensure the string is not entirely composed of whitespace characters.
   * (?: ...): Non-capturing group for the entire email pattern.
   * [a-zA-Z0-9._%+-]+: Matches one or more alphanumeric characters, dots, underscores, percentage signs, plus signs, or hyphens.
   * @: Matches the "@" symbol.
   * [a-zA-Z0-9.-]+: Matches one or more alphanumeric characters, dots, or hyphens for the domain name.
   * \.: Matches a literal dot.
   * [a-zA-Z]{2,}: Matches at least two alphabetical characters for the top-level domain (TLD).
   */
  const regex = Data_Structure?.value?.[Current_Step?.value]?.regex
  return !regex.test(Data_Structure?.value?.[Current_Step?.value]?.value)
})

/**
 * this method check if field is invalid fill the error message
 * @description first clear error message to revalidate
 */
function validateField() {
  Data_Structure.value[Current_Step.value].error = '' // Clear previous error
  if (isFieldInvalid.value)
    Data_Structure.value[Current_Step.value].error = ERRORS_MSG[Current_Step.value]
}

/**
 * this method update result after all logics passed
 * @description just for clear result functionality
 */
function updateModel(value) {
  switch (Current_Step.value) {
    case 0:
      Model.value.username = value
      break
    case 1:
      Model.value.email = value
      break
    default:
      break
  }
}

/**
 * this method call validation and if form is valid form goes to next step
 * @description just check validation for next step and when step is not the last
 */
function submitForm(is_next) {
  if (is_next) {
    // check current field is valid
    validateField()
    if (!Data_Structure?.value?.[Current_Step?.value]?.error) {
      // update model and submit form
      updateModel(Data_Structure?.value?.[Current_Step?.value]?.value)
      ++Current_Step.value
    }
  }
  // in prev button no need check validation
  else { --Current_Step.value }
}
</script>

<template>
  <form class="flex flex-col items-center justify-center gap-8 border border-gray-700 rounded-xl">
    <!-- Page Header -->
    <div class="w-full rounded-t-xl bg-gray-700 p-5 text-center text-2xl text-emerald-500 font-semibold">
      {{ Headers[Current_Step] }}
    </div>

    <!-- Review -->
    <div v-if="Current_Step === 2" class="w-full flex flex-col items-center justify-center gap-4 p-4 text-white">
      <span>Username: <b>{{ Model?.username }}</b></span>
      <span>Email: <b>{{ Model?.email }}</b></span>
    </div>

    <!-- Inputs -->
    <div v-else class="w-full p-4">
      <label class="text-white" :for="Data_Structure[Current_Step].id" >
        {{ Data_Structure[Current_Step].label }}
      </label>
      <input
        :id="Data_Structure[Current_Step].id"
        v-model="Data_Structure[Current_Step].value"
        :name="Data_Structure[Current_Step].name"
        :placeholder="Data_Structure[Current_Step].placeholder"
        type="text"
        class="w-full border-0 rounded-md bg-gray-100 px-3 pb-1.5 pt-2.5 text-gray-900 shadow-sm ring-1 ring-gray-300 ring-inset focus-within:ring-2 focus-within:ring-emerald-600"
        @blur="validateField"
      >
      <span v-if="Data_Structure[Current_Step].error" class="text-xs text-red-500">
        {{ Data_Structure[Current_Step].error }}
      </span>
    </div>

    <!-- Buttons -->
    <div class="w-full flex items-center justify-between gap-2 border-t border-t-gray-700 rounded-b-xl p-4">
      <!-- Prev -->
      <button
        id="btn-prev"
        type="button"
        class="rounded bg-emerald-400/50 px-2 py-1 text-white disabled:bg-gray-500"
        :disabled="Current_Step === 0"
        @click="submitForm(false)"
      >
        {{ '<- Previous' }}
      </button>

      <!-- Next -->
      <button
        id="btn-next"
        type="button"
        class="rounded bg-emerald-400/50 px-2 py-1 text-white disabled:bg-gray-500"
        :disabled="Current_Step === 2"
        @click="submitForm(true)"
      >
        {{ 'Next ->' }}
      </button>
    </div>
  </form>
</template>
