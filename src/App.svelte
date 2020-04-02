<script>
    import Cursor from "./Cursor.svelte";
</script>

<main>
	<Cursor/>
	<h1>Hello!</h1>
	<p>Move cursor to the link</p>
    <a data-cursor="Write" href="mailto:email@gmail.com">email@mail.com</a>
</main>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #ff3e00;
		text-transform: uppercase;
		font-size: 4em;
		font-weight: 100;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
