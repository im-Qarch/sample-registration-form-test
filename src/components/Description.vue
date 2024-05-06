<script>
/**
 * ? Use Options API Syntax
 *
 * Because it was said in  Readme not to modify the tests,
 * I had to use Syntax Options API for the test :)
 * cause the tests had to be written differently in Composition API.
 */

// Debounce function to delay execution of a function
function debounce(func, delay) {
  let timer
  return function () {
    const context = this
    const args = arguments
    clearTimeout(timer)
    timer = setTimeout(() => {
      func.apply(context, args)
    }, delay)
  }
}

export default {
  data() {
    return {
      text: '',
      strIsBalance: '',
      balancedMessage: 'The text is balanced.',
      UnbalancedMessage: 'The text is not balanced.',
    }
  },
  methods: {
    handleInput: debounce(function () {
      this.isBalanced()
    }, 200), // Adjust the debounce delay as needed

    isBalanced() {
      const stack = []
      const matchingBrackets = {
        ')': '(',
        '}': '{',
        ']': '[',
      }
      // check the whole string and return the proper message
      for (const char of this.text) {
        if (char === '(' || char === '[' || char === '{') {
          stack.push(char)
        }
        else if (char === ')' || char === ']' || char === '}') {
          if (stack.length === 0 || stack.pop() !== matchingBrackets[char])
            this.strIsBalance = false
        }
      }

      this.strIsBalance = stack.length === 0
    },
  },
}
</script>

<template>
  <div class="mt-10 flex flex-col items-center justify-center gap-8 border border-gray-700 rounded-xl p-5">
    <input
      id="description"
      v-model="text"
      name="description"
      type="text"
      placeholder="Write a sample text"
      class="w-full border-0 rounded-md bg-gray-100 px-3 pb-1.5 pt-2.5 text-gray-900 shadow-sm ring-1 ring-gray-300 ring-inset focus-within:ring-2 focus-within:ring-emerald-600"
      @input="handleInput"
    >
    <span class="text-lg text-green-500" :class="{ '!text-red-500': !strIsBalance }">
      {{ strIsBalance ? balancedMessage : UnbalancedMessage }}
    </span>
  </div>
</template>
