<script lang="ts">
  import '../app.css';

  import { quintOut } from 'svelte/easing';
  import { crossfade } from 'svelte/transition';
  import { flip } from 'svelte/animate';

  const [send, receive] = crossfade({
    duration: (d) => Math.sqrt(d * 200),

    fallback(node, params) {
      const style = getComputedStyle(node);
      const transform = style.transform === 'none' ? '' : style.transform;

      return {
        duration: 600,
        easing: quintOut,
        css: (t) => `
                    transform: ${transform} scale(${t});
                    opacity: ${t}
                `
      };
    }
  });

  let input: string = '';

  let placeholder: string = 'Type here to see the leetspeak conversion.';
  let copied = false;

  // todo: use with a "reset" button to set enabled state properly
  // let basicLeet = ['A', 'B', 'E', 'G', 'I', 'O', 'Q', 'S', 'T', 'Z'];

  let uid = 1;

  let leetTable = {
    A: { id: uid++, label: 'A', enabled: true, selected: '4', menu: ['4', '@'] },
    B: { id: uid++, label: 'B', enabled: true, selected: '8', menu: ['8', '|3'] },
    C: { id: uid++, label: 'C', enabled: true, selected: 'K', menu: ['K', '(', '<', '{'] },
    D: { id: uid++, label: 'D', enabled: false, selected: '|)', menu: ['|)', '|>', '|}'] },
    E: { id: uid++, label: 'E', enabled: true, selected: '3', menu: ['3', '£'] },
    F: { id: uid++, label: 'F', enabled: false, selected: '|=', menu: ['|=', '|#'] },
    G: { id: uid++, label: 'G', enabled: true, selected: '6', menu: ['6', '9'] },
    H: { id: uid++, label: 'H', enabled: false, selected: '|-|', menu: ['|-|', '#-'] },
    I: { id: uid++, label: 'I', enabled: true, selected: '1', menu: ['1', '!', '|'] },
    J: { id: uid++, label: 'J', enabled: false, selected: '_|', menu: ['_|', '_/'] },
    K: { id: uid++, label: 'K', enabled: false, selected: '|<', menu: ['|<', '|{'] },
    L: { id: uid++, label: 'L', enabled: false, selected: '|_', menu: ['|_', '|'] },
    M: { id: uid++, label: 'M', enabled: false, selected: '|V', menu: ['|V', '|\\/|'] },
    N: { id: uid++, label: 'N', enabled: false, selected: '|\\|', menu: ['|\\|', '|\\/'] },
    O: { id: uid++, label: 'O', enabled: true, selected: '0', menu: ['0', '()', '[]'] },
    P: { id: uid++, label: 'P', enabled: false, selected: '|*', menu: ['|*', '|>'] },
    Q: { id: uid++, label: 'Q', enabled: false, selected: '9', menu: ['9', '(,)'] },
    R: { id: uid++, label: 'R', enabled: false, selected: '|2', menu: ['|2', '|?'] },
    S: { id: uid++, label: 'S', enabled: true, selected: '5', menu: ['5', '$'] },
    T: { id: uid++, label: 'T', enabled: true, selected: '7', menu: ['7', '+'] },
    U: { id: uid++, label: 'U', enabled: false, selected: '|_|', menu: ['|_|', '|_\\|'] },
    V: { id: uid++, label: 'V', enabled: false, selected: '\\/', menu: ['\\/'] },
    W: { id: uid++, label: 'W', enabled: false, selected: '\\/\\/', menu: ['\\/\\/'] },
    X: { id: uid++, label: 'X', enabled: false, selected: '><', menu: ['><'] },
    Y: { id: uid++, label: 'Y', enabled: false, selected: '`/', menu: ['`/'] },
    Z: { id: uid++, label: 'Z', enabled: true, selected: '2', menu: ['2'] }
  };

  $: {
    copied = false;
    input = input;
    leetTable = leetTable;
  }

  $: {
    leetTable = Object.fromEntries(
      Object.entries(leetTable).sort(([aKey, aVal], [bKey, bVal]) => {
        if (aVal.enabled && !bVal.enabled) {
          return -1;
        }
        if (!aVal.enabled && bVal.enabled) {
          return 1;
        }
        return aKey.localeCompare(bKey);
      })
    ) as typeof leetTable;
  }

  $: leeted = Array.from(input || placeholder)
    .map((c) => {
      const leetTableEntry = leetTable[c.toUpperCase()];
      if (!leetTableEntry?.enabled) {
        return Math.random() > 0.5 ? c : c.toUpperCase();
      }
      return leetTableEntry.selected;
    })
    .join('');
  function copyLeeted() {
    navigator.clipboard.writeText(leeted);
    copied = true;
  }
</script>

<h1 class="mb-5 lg:mb-16 lg:mt-5 text-center font-semibold text-xl md:text-2xl dark:text-white">
  A leetspeak converter.
</h1>

<!-- svelte-ignore a11y-autofocus -->
<textarea
  type="text"
  bind:value={input}
  {placeholder}
  autofocus
  class="text-lg dark:text-gray-300 p-2 mb-3 w-full ring-1 ring-slate-900/10 shadow-sm placeholder:text-black dark:placeholder:text-slate-400 rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 caret-indigo-500 
    dark:bg-slate-800 dark:ring-0 dark:highlight-white/5 dark:ring-2 dark:ring-indigo-800 dark:focus:ring-indigo-500 dark:focus:bg-slate-900"
/>
<div
  class="p-1 mx-auto text-md text-black dark:text-white rounded-lg bg-indigo-50/70 dark:bg-slate-700/30 cursor-pointer hover:bg-indigo-200/40 dark:hover:bg-slate-800"
  on:click={copyLeeted}
>
  <p
    class="font-mono text-lg font-semibold mx-6 mt-6 mb-2 p-2 ring-1 rounded-md w-xl dark:text-gray-300 dark:highlight-white/5 dark:bg-slate-800 cursor-pointer"
  >
    {leeted}
  </p>
  <p
    class:copied={!copied}
    class={`select-none text-center cursor-pointer text-blue-600 dark:text-blue-400 hover:text-blue-500 pb-1 
        ${copied ? ' hidden' : ''}`}
  >
    click to copy leet text
  </p>
  <p
    class:copied
    class={`select-none text-center font-bold text-emerald-600 dark:text-emerald-300 pb-1
        ${copied ? '' : ' hidden'}`}
  >
    leet text copied to clipboard
  </p>
</div>

<div class="flex flex-wrap gap-2 md:gap-4 pt-5 md:pt-10">
  {#each Object.values(leetTable) as leet (leet.id)}
    <div
      in:receive={{ key: leet.id }}
      out:send={{ key: leet.id }}
      animate:flip={{ duration: 200 }}
      class={`flex-none flex items-center space-x-2 lg:space-x-3 ring-1 p-3 rounded-md cursor-pointer hover:bg-indigo-100 dark:hover:bg-slate-800 ${
        leet.enabled
          ? 'bg-indigo-200 hover:bg-indigo-200 dark:bg-indigo-600 ring-indigo-400 dark:hover:ring-indigo-500 dark:hover:bg-indigo-700'
          : ''
      }`}
      on:click={() => (leet.enabled = !leet.enabled)}
    >
      <span
        class="select-none dark:text-white text-slate-900 text-xl sm:text-3xl md:text-4xl lg:text-5xl font-bold dark:text-slate-200"
        >{leet.label}</span
      >
      <select
        bind:value={leet.selected}
        class="dark:bg-slate-800 dark:text-white rounded overflow-auto max-w-6 sm:w-3 md:w-8 lg:w-10 font-mono"
        on:click={(e) => e.stopPropagation()}
      >
        {#each leet.menu as item}
          <option value={item} selected={item === leet.selected}>{item}</option>
        {/each}
      </select>
    </div>
  {/each}
</div>
