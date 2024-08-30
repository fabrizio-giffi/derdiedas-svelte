<script>
	/** @type {import('./$types').PageData} */
	export let data;

	import capitalize from 'capitalize';

	const GENDERMAP = {
		m: { tag: 'Maskulin', article: 'der' },
		f: { tag: 'Feminin', article: 'die' },
		n: { tag: 'Neutral', article: 'das' }
	};

	$: qWord = '';
	$: gender = '';
	$: errMsg = '';
	$: show = 'hidden';

	async function search(event) {
		// Reset states
		errMsg = '';
		gender = '';

		const data = new FormData(event.currentTarget);
		qWord = capitalize(data.get('w'));

		const qProps = '&prop=extracts&explaintext&exchars=100&format=json&origin=*';
		const response = await fetch(
			`https://de.wiktionary.org/w/api.php?action=query&titles=${qWord}${qProps}`
		);
		const body = await response.json();
		// No results
		if (body.query.pages['-1']) {
			errMsg = 'No results found. Have you entered more than a word or special characters?';
			return;
		}
		// Text extract from Wiktionary response
		const text = await Object.values(body.query.pages)[0].extract;
		// Results found but invalid return body. Most probably plural forms of a word
		if (text.includes('=== Deklinierte Form ===')) {
			errMsg = 'Invalid query. Have you entered the plural form of the word?';
			return;
		}
		// Parse the text to get the gender
		gender = text.split('Substantiv, ')[1][0];
	}
</script>

<form
	class="flex flex-col gap-2 items-center my-5 mx-auto w-3/5"
	on:submit|preventDefault={(event) => search(event)}
>
	<div class="flex bg-white border rounded-md w-full">
		<input
			class="w-11/12 py-2 pl-4 field"
			type="text"
			name="w"
			value={qWord}
			placeholder="Type a word to get the gender reveal"
			required
		/>
		<button
			type="submit"
			class="icon bg-zinc-800 p-1 w-1/12 rounded-md hover:scale-110 hover:border hover:border-white"
		>
			<i class="fa fa-search" />
		</button>
	</div>
	<small class="flex gap-2 items-center w-full">
		<i class="fas fa-info-circle mr-1 text-white"></i>
		<span> Input field is case-insensitive </span>
		<span class="mx-5"> — </span>
		<span> Ä ä Ö ö Ü ü ß allowed</span>
	</small>
</form>

{#if errMsg}
	<div
		class="flex items-center justify-center w-3/5 mx-auto gap-3 py-4 px-6 border text-red-200 border-red-200 rounded-md"
	>
		<i class="fa fa-exclamation-triangle" aria-hidden="true"></i>
		<p>{errMsg}</p>
	</div>
{/if}

{#if gender}
	<section class="rounded-md border border-white py-4 px-6 w-3/5 mx-auto flex justify-between">
		<div>
			<h3 class="text-4xl mb-2">{GENDERMAP[gender].article}</h3>
			<h4 class="text-2xl mb-1">{qWord}</h4>
			<p>Substantiv, {GENDERMAP[gender].tag}</p>
		</div>
		<div class="flex flex-col text-end">
			<a
				class="uppercase hover:underline hover:decoration-2 text-violet-300"
				target="_blank"
				href={`https://www.duden.de/suchen/dudenonline/${qWord}`}
			>
				definition - duden
			</a>
			<a
				class="uppercase hover:underline hover:decoration-2 text-violet-300"
				target="_blank"
				href={`https://www.deepl.com/de/translator#de/en-us/${qWord}`}
			>
				translation - deepl
			</a>
		</div>
	</section>
{/if}

<section
	on:mouseenter={() => (show = 'grid')}
	on:mouseleave={() => (show = 'hidden')}
	role="presentation"
	class="absolute bottom-0 left-1/4 right-1/4 py-4 px-6 border-x border-t border-white rounded-t-md"
>
	<h3 class={"text-2xl text-center mb-3"}>Usual suspects</h3>
	<div class={`${show} grid-cols-3 justify-items-center`}>
		<div>
			<h4 class="text-xl font-bold">Maskulin</h4>
			<ul>
				{#each data.m as entry}
					<li>
						{entry}
					</li>
				{/each}
			</ul>
		</div>
		<div>
			<h4 class="text-xl font-bold">Feminin</h4>
			<ul>
				{#each data.f as entry}
					<li>
						{entry}
					</li>
				{/each}
			</ul>
		</div>
		<div>
			<h4 class="text-xl font-bold">Neutral</h4>
			<ul>
				{#each data.n as entry}
					<li>
						{entry}
					</li>
				{/each}
			</ul>
		</div>
	</div>
</section>
