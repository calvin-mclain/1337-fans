<script lang="ts">
	import '../app.css';
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

	let leetTable = {
		A: { enabled: true, selected: ['4'], menu: ['4', '@'] },
		B: { enabled: true, selected: ['8'], menu: ['8', '|3'] },
		C: { enabled: false, selected: ['('], menu: ['(', '<', '{'] },
		D: { enabled: false, selected: ['|)'], menu: ['|)', '|>', '|}'] },
		E: { enabled: true, selected: ['3'], menu: ['3', '£'] },
		F: { enabled: false, selected: ['|='], menu: ['|=', '|#'] },
		G: { enabled: true, selected: ['6'], menu: ['6', '9'] },
		H: { enabled: false, selected: ['|-|'], menu: ['|-|', '#-'] },
		I: { enabled: true, selected: ['1'], menu: ['1', '!', '|'] },
		J: { enabled: false, selected: ['_|'], menu: ['_|', '_/'] },
		K: { enabled: false, selected: ['|<'], menu: ['|<', '|{'] },
		L: { enabled: false, selected: ['|_'], menu: ['|_', '|'] },
		M: { enabled: false, selected: ['|V'], menu: ['|V', '|\\/|'] },
		N: { enabled: false, selected: ['|\\|'], menu: ['|\\|', '|\\/'] },
		O: { enabled: true, selected: ['0'], menu: ['0', '()', '[]'] },
		P: { enabled: false, selected: ['|*'], menu: ['|*', '|>'] },
		Q: { enabled: true, selected: ['9'], menu: ['9', '(,)'] },
		R: { enabled: false, selected: ['|2'], menu: ['|2', '|?'] },
		S: { enabled: true, selected: ['5'], menu: ['5', '$'] },
		T: { enabled: true, selected: ['7'], menu: ['7', '+'] },
		U: { enabled: false, selected: ['|_|'], menu: ['|_|', '|_\\|'] },
		V: { enabled: false, selected: ['\\/'], menu: ['\\/'] },
		W: { enabled: false, selected: ['\\/\\'], menu: ['\\/\\/'] },
		X: { enabled: false, selected: ['><'], menu: ['><'] },
		Y: { enabled: false, selected: ['`/'], menu: ['`/'] },
		Z: { enabled: true, selected: ['2'], menu: ['2'] }
	};

	$: leeted = Array.from((input || placeholder).toUpperCase())
		.map((c) => {
			const leetTableEntry = leetTable[c];
			if (!leetTableEntry?.enabled) {
				return c;
			}
			return leetTableEntry.selected[Math.floor(Math.random() * leetTableEntry.selected.length)];
		})
		.join('');

	$: sortedLeetKeys = Object.keys(leetTable).sort((a, b) => a.localeCompare(b));
</script>

<textarea
	type="text"
	bind:value={input}
	{placeholder}
	autofocus
	class="text-lg text-gray-300 p-2 mb-3 w-full ring-1 ring-slate-900/10 shadow-sm rounded-md focus:outline-none  focus:ring-2 focus:ring-indigo-500 caret-indigo-500 
    dark:bg-slate-800 dark:ring-0 dark:highlight-white/5 dark:ring-2 dark:ring-indigo-800 dark:focus:ring-indigo-500 dark:focus:bg-slate-900"
/>
<p
	class="select-all p-6 mb-5 w-md mx-auto text-md text-black dark:text-white rounded-lg bg-slate-700"
>
	{leeted}
</p>

<div class="flex flex-wrap gap-4 p-5">
	{#each Object.entries(leetTable) as [key, value]}
		<div
			class={`flex-none flex items-center ring-1 p-1 rounded-md cursor-pointer ${
				value.enabled ? 'bg-indigo-700' : ''
			}`}
			on:click={() => (value.enabled = !value.enabled)}
		>
			<input type="checkbox" bind:checked={value.enabled} class="appearance-none" />
			<span class="select-none pr-3 text-slate-900 text-6xl font-bold dark:text-slate-200">{key}</span>
			<select
				multiple
				bind:value={value.selected}
				class="dark:bg-slate-800 dark:text-white rounded overflow-auto w-10 font-mono"
                on:click={e => e.stopPropagation()}
			>
				{#each value.menu as item}
					<option value={item}>{item}</option>
				{/each}
			</select>
		</div>
	{/each}
</div>
