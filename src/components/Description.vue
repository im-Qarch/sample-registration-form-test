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
    }, 300), // Adjust the debounce delay as needed with or without consider test cases

    isBalanced() {
      const open_stack = []
      const close_stack = []
      const open_brackets = '({['
      const close_brackets = ')}]'
      const mapping_brackets_close = {
        ')': '(',
        '}': '{',
        ']': '[',
      };

      // convert text to array and loop over all to exclude  open_brackets and close_brackets character
      [...this.text].forEach((char) => {
        if (open_brackets.includes(char))
          open_stack.push(char) // ex: ['(','[']

        else if (close_brackets.includes(char))
          close_stack.unshift(char) // ex: [']','}']
      })

      // then check in these condition
      // first: level not matches in any case , return to no calculate more like: {test[test]
      if (open_stack.length !== close_stack.length)
        this.strIsBalance = false

      // then: level is matched but priorities not the same like: {test[test}]
      else if (!open_stack.every((open_symbol, i) => open_symbol === mapping_brackets_close[close_stack[i]]))
        this.strIsBalance = false

      // otherwise: all good :)
      else this.strIsBalance = true
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
