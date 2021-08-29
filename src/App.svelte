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
                "s": "1BvyP8VZLKEQYKus7oLL1s3r9MQRLXz5pb",
                "i": 0
            }
        }
     },
    "project": {
        "out.tape.cell.$": 1
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
	.then(res => {
		const reader = res.body.getReader()
		return reader.read()
	})
	.then( ({ value }) => {
		const str = new TextDecoder().decode(value);
		const txArray = JSON.parse(str)
		// console.log(txArray)
		return Promise.all(txArray.map(tx => {
			const cell = tx.out[0].tape[1].cell[1]
			if (cell.f) {
				return fetch('https://x.bitfs.network/' + cell.f)
					.then(res => {
						const reader = res.body.getReader()
						return reader.read()
					})
					.then(({value}) => {
						return new TextDecoder().decode(value)
						// return JSON.parse(new TextDecoder().decode(value))
					})
			}
			return Promise.resolve(cell.s)
		}))
	})
	.then(jsonArr => { 
		posts = jsonArr.map(json => {
			const parsed = JSON.parse(json)
			return parsed
		})
		console.log(posts)
	})
</script>

<div id="container">
	<header>
		<ul class="breadcrumbs">
			<li on:click={() => page = 'home'}>home</li>
			{#if page != 'home'}
				<li>&gt;</li>
				<li>{posts[page].title}</li>
			{/if}
		</ul>

		<h1>My Awesome Svelte App</h1>
	</header>

	<main>
		{#if page == 'home'}
			<BlogView posts={posts} bind:page={page}/>
		{:else}
			<PostView post={posts[page]}/>
		{/if}
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
		font-weight: 400;
		/* font-size: 3rem; */
		padding: 2rem 0;
	}
	.breadcrumbs {
		display: flex;
		padding: 1rem 0;
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
