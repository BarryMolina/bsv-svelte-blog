<script>
	export let posts
	export let page
</script>

<div id="blog-view">
	<ul id="post-list">
		{#each posts as post, i}
			<li on:click={() => {
				page = i
				console.log(page)
			}
			}>{@html post.title}</li>
		{/each}
	</ul>
</div>

<style>
	* {
		margin: 0;
		padding: 0;
	}
	ul {
		/* max-width: 500px; */
		list-style-type: none;
	}
	li {
		padding: 1rem;

	}
	li:hover {
		cursor: pointer;
		background-color: antiquewhite;
		color: black;
	}


</style>