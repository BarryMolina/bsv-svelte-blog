<script>
	import PostView from './components/PostView.svelte'
	import BlogView from './components/BlogView.svelte'

	let posts = []
	let page = 'home'
	const token = process.env.token
	const query = {
  "q": {
    "find": {
        "in.e.a": "1BvyP8VZLKEQYKus7oLL1s3r9MQRLXz5pb",
        "out.tape.cell": {
            "$elemMatch": {
                "s": "Gendale",
                "i": 0
            }
        }
     },
    "project": {
        "out.tape.cell.s.$": 1
    },
    "limit": 5
  }
}
fetch('https://bob.bitbus.network/block', {
	method: 'post',
	headers: {
		'Content-type': 'application/json; charset=utf-8',
		'token': token,
		'format': 'json'
	},
	body: JSON.stringify(query)
})
	// Retrieve the body as a ReadableStream
	.then(async res => {
		const reader = res.body.getReader()
		return reader.read()
	})
	.then( ({ value }) => {
		const str = new TextDecoder().decode(value);
		const txArray = JSON.parse(str)
		posts = txArray.map(tx => tx.out[0].tape[1].cell[1].s)
	})
</script>

<div id="container">
	<header>
		<ul class="breadcrumbs">
			<li on:click={() => page = 'home'}>home</li>
			<li>{page}</li>
		</ul>

		<h1>My Svelte App</h1>
	</header>

	<main>
		<BlogView posts={posts} bind:page={page}/>
	</main>

	<footer></footer>
</div>

<style>
	#container {
		max-width: 800px;
		margin: 0 auto;
		padding: 100px 0;
	}
	header {
		padding: 1rem 0;
	}
	main {
		padding: 1rem 0;
	}
	h1 {
			color: white;
	}
	.breadcrumbs {
		display: flex;
		padding: 1rem 0;
		color: white;
		list-style-type: none;
	}
	.breadcrumbs li:not(:first-child) {
		margin-left: 1rem;
	}
	.breadcrumbs li:hover {
		cursor: pointer;
		text-decoration: underline;
	}
	/* p {
		color: white;
	} */
</style>
