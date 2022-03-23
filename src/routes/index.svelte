<script lang="ts">
	import '../app.css';

	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),

		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		}
	});
	let input: string = '';

	const panagrams = [
		"This pangram lists four a's, one b, one c, two d's, twenty-nine e's, eight f's, three g's, five h's, eleven i's, one j, one k, three l's, two m's, twenty-two n's, fifteen o's, one p, one q, seven r's, twenty-six s's, nineteen t's, four u's, five v's, nine w's, two x's, four y's, and one z.",
		'The quick brown fox jumps over the lazy dog.',
		'The five boxing wizards jump quickly.',
		'Sphinx of black quartz, judge my vow.',
		'Pack my box with five dozen liquor jugs.',
		'How vexingly quick daft zebras jump!',
		'Mr. Jock, TV quiz PhD., bags few lynx.',
		'Two driven jocks help fax my big quiz.',
		'Brown jars prevented the mixture from freezing too quickly.',
		'Watch “Jeopardy!”, Alex Trebek’s fun TV quiz game.',
		'When zombies arrive, quickly fax Judge Pat.',
		'Waxy and quivering, jocks fumble the pizza.',
		'The jay, pig, fox, zebra, and my wolves quack!',
		'The five boxing wizards jump quickly.',
		'Farmer Jack realized that big yellow quilts were expensive.'
	];

	let placeholder: string = panagrams[Math.floor(Math.random() * panagrams.length)];

	// todo: use with a "reset" button to set enabled state properly
	// let basicLeet = ['A', 'B', 'E', 'G', 'I', 'O', 'Q', 'S', 'T', 'Z'];

    let uid = 1;

	let leetTable = {
		A: { id: uid++, label: 'A', enabled: true, selected: ['4'], menu: ['4', '@'] },
		B: { id: uid++, label: 'B', enabled: true, selected: ['8'], menu: ['8', '|3'] },
		C: { id: uid++, label: 'C', enabled: false, selected: ['('], menu: ['(', '<', '{'] },
		D: { id: uid++, label: 'D', enabled: false, selected: ['|)'], menu: ['|)', '|>', '|}'] },
		E: { id: uid++, label: 'E', enabled: true, selected: ['3'], menu: ['3', '£'] },
		F: { id: uid++, label: 'F', enabled: false, selected: ['|='], menu: ['|=', '|#'] },
		G: { id: uid++, label: 'G', enabled: true, selected: ['6'], menu: ['6', '9'] },
		H: { id: uid++, label: 'H', enabled: false, selected: ['|-|'], menu: ['|-|', '#-'] },
		I: { id: uid++, label: 'I', enabled: true, selected: ['1'], menu: ['1', '!', '|'] },
		J: { id: uid++, label: 'J', enabled: false, selected: ['_|'], menu: ['_|', '_/'] },
		K: { id: uid++, label: 'K', enabled: false, selected: ['|<'], menu: ['|<', '|{'] },
		L: { id: uid++, label: 'L', enabled: false, selected: ['|_'], menu: ['|_', '|'] },
		M: { id: uid++, label: 'M', enabled: false, selected: ['|V'], menu: ['|V', '|\\/|'] },
		N: { id: uid++, label: 'N', enabled: false, selected: ['|\\|'], menu: ['|\\|', '|\\/'] },
		O: { id: uid++, label: 'O', enabled: true, selected: ['0'], menu: ['0', '()', '[]'] },
		P: { id: uid++, label: 'P', enabled: false, selected: ['|*'], menu: ['|*', '|>'] },
		Q: { id: uid++, label: 'Q', enabled: true, selected: ['9'], menu: ['9', '(,)'] },
		R: { id: uid++, label: 'R', enabled: false, selected: ['|2'], menu: ['|2', '|?'] },
		S: { id: uid++, label: 'S', enabled: true, selected: ['5'], menu: ['5', '$'] },
		T: { id: uid++, label: 'T', enabled: true, selected: ['7'], menu: ['7', '+'] },
		U: { id: uid++, label: 'U', enabled: false, selected: ['|_|'], menu: ['|_|', '|_\\|'] },
		V: { id: uid++, label: 'V', enabled: false, selected: ['\\/'], menu: ['\\/'] },
		W: { id: uid++, label: 'W', enabled: false, selected: ['\\/\\'], menu: ['\\/\\/'] },
		X: { id: uid++, label: 'X', enabled: false, selected: ['><'], menu: ['><'] },
		Y: { id: uid++, label: 'Y', enabled: false, selected: ['`/'], menu: ['`/'] },
		Z: { id: uid++, label: 'Z', enabled: true, selected: ['2'], menu: ['2'] }
	};

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

	$: leeted = Array.from((input || placeholder).toUpperCase())
		.map((c) => {
			const leetTableEntry = leetTable[c];
			if (!leetTableEntry?.enabled) {
				return c;
			}
			return leetTableEntry.selected[Math.floor(Math.random() * leetTableEntry.selected.length)];
		})
		.join('');
</script>

<!-- svelte-ignore a11y-autofocus -->
<textarea
	type="text"
	bind:value={input}
	{placeholder}
	autofocus
	class="text-lg dark:text-gray-300 p-2 mb-3 w-full ring-1 ring-slate-900/10 shadow-sm rounded-md focus:outline-none focus:ring-2 focus:ring-indigo-500 caret-indigo-500 
    dark:bg-slate-800 dark:ring-0 dark:highlight-white/5 dark:ring-2 dark:ring-indigo-800 dark:focus:ring-indigo-500 dark:focus:bg-slate-900"
/>
<p
	class="select-all p-6 font-mono mb-5 w-md mx-auto text-md text-black dark:text-white rounded-lg bg-slate-200 dark:bg-slate-700"
>
	{leeted}
</p>

<div class="flex flex-wrap gap-4 p-5">
	{#each Object.values(leetTable) as leet (leet.id)}
		<div
            in:receive={{key: leet.id}}
            out:send={{key: leet.id}}
            animate:flip="{{duration: 200}}"
			class={`flex-none flex items-center ring-1 p-1 rounded-md cursor-pointer ${
				leet.enabled ? 'bg-indigo-400 dark:bg-indigo-700' : ''
			}`}
			on:click={() => (leet.enabled = !leet.enabled)}
		>
			<input type="checkbox" bind:checked={leet.enabled} class="appearance-none" />
			<span class="select-none pr-3 dark:text-white text-slate-900 text-6xl font-bold dark:text-slate-200"
				>{leet.label}</span
			>
			<select
				multiple
				bind:value={leet.selected}
				class="dark:bg-slate-800 dark:text-white rounded overflow-auto w-10 font-mono"
				on:click={(e) => e.stopPropagation()}
			>
				{#each leet.menu as item}
					<option value={item}>{item}</option>
				{/each}
			</select>
		</div>
	{/each}
</div>
