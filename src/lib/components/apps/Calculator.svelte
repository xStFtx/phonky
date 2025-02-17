<script lang="ts">
  let display = '0';
  let previousValue = '';
  let operation = '';
  let newNumber = true;

  function addDigit(digit: string): void {
    if (newNumber) {
      display = digit;
      newNumber = false;
    } else {
      display = display === '0' ? digit : display + digit;
    }
  }

  function addDecimal(): void {
    if (!display.includes('.')) {
      display += '.';
      newNumber = false;
    }
  }

  function setOperation(op: string): void {
    operation = op;
    previousValue = display;
    newNumber = true;
  }

  function calculate(): void {
    const prev = parseFloat(previousValue);
    const current = parseFloat(display);
    let result = 0;

    switch (operation) {
      case '+':
        result = prev + current;
        break;
      case '-':
        result = prev - current;
        break;
      case '×':
        result = prev * current;
        break;
      case '÷':
        result = prev / current;
        break;
    }

    display = result.toString();
    operation = '';
    newNumber = true;
  }

  function clear(): void {
    display = '0';
    previousValue = '';
    operation = '';
    newNumber = true;
  }

  function percentage(): void {
    display = (parseFloat(display) / 100).toString();
  }

  function toggleSign(): void {
    display = (parseFloat(display) * -1).toString();
  }
</script>

<div class="h-full bg-gray-900 p-5 flex flex-col gap-5">
  <div 
    class="bg-black/20 p-5 text-right text-5xl font-light text-white rounded-2xl min-h-[100px] flex items-center justify-end break-all backdrop-blur-lg"
    role="textbox" 
    aria-label="Calculator display"
  >
    {display}
  </div>
  
  <div class="grid grid-cols-4 gap-3 flex-grow">
    <button 
      class="rounded-full bg-gray-400 text-black text-2xl flex items-center justify-center hover:bg-gray-300 active:scale-95 transition-all"
      on:click={clear}
    >
      C
    </button>
    <button 
      class="rounded-full bg-gray-400 text-black text-2xl flex items-center justify-center hover:bg-gray-300 active:scale-95 transition-all"
      on:click={toggleSign}
    >
      ±
    </button>
    <button 
      class="rounded-full bg-gray-400 text-black text-2xl flex items-center justify-center hover:bg-gray-300 active:scale-95 transition-all"
      on:click={percentage}
    >
      %
    </button>
    <button 
      class="rounded-full bg-amber-500 text-white text-2xl flex items-center justify-center hover:bg-amber-400 active:scale-95 transition-all"
      on:click={() => setOperation('÷')}
    >
      ÷
    </button>
    
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('7')}
    >
      7
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('8')}
    >
      8
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('9')}
    >
      9
    </button>
    <button 
      class="rounded-full bg-amber-500 text-white text-2xl flex items-center justify-center hover:bg-amber-400 active:scale-95 transition-all"
      on:click={() => setOperation('×')}
    >
      ×
    </button>
    
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('4')}
    >
      4
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('5')}
    >
      5
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('6')}
    >
      6
    </button>
    <button 
      class="rounded-full bg-amber-500 text-white text-2xl flex items-center justify-center hover:bg-amber-400 active:scale-95 transition-all"
      on:click={() => setOperation('-')}
    >
      -
    </button>
    
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('1')}
    >
      1
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('2')}
    >
      2
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('3')}
    >
      3
    </button>
    <button 
      class="rounded-full bg-amber-500 text-white text-2xl flex items-center justify-center hover:bg-amber-400 active:scale-95 transition-all"
      on:click={() => setOperation('+')}
    >
      +
    </button>
    
    <button 
      class="col-span-2 rounded-full bg-gray-800 text-white text-2xl flex items-center justify-start pl-8 hover:bg-gray-700 active:scale-95 transition-all"
      on:click={() => addDigit('0')}
    >
      0
    </button>
    <button 
      class="rounded-full bg-gray-800 text-white text-2xl flex items-center justify-center hover:bg-gray-700 active:scale-95 transition-all"
      on:click={addDecimal}
    >
      .
    </button>
    <button 
      class="rounded-full bg-amber-500 text-white text-2xl flex items-center justify-center hover:bg-amber-400 active:scale-95 transition-all"
      on:click={calculate}
    >
      =
    </button>
  </div>
</div>

<style>
  /* Removed existing styles */
</style>
