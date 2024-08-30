<script>
	import '../app.css';
	import '@fortawesome/fontawesome-free/css/all.min.css';
</script>

<main class="max-w-screen-lg mx-auto">
	<nav class="py-5 flex items-center justify-between">
		<div class="flex gap-2 items-baseline">
			<h1 class="text-2xl">DerDieDas</h1>
			<span class="self-center">//</span>
			<p>Answering the real questions</p>
		</div>
		<a href="/">
			<i class="fa-solid fa-house"> </i>
		</a>
	</nav>

	<slot />
</main>
