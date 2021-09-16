<script>
	import { ApolloClient } from "@apollo/client";
	import { setClient } from "svelte-apollo";
	import { query } from "svelte-apollo";
	import { InMemoryCache } from "apollo-cache-inmemory";
	import { HttpLink } from "apollo-link-http";
	import gql from 'graphql-tag'
	
	const link = new HttpLink({
		uri: process.env.SVELTE_APP_TAKESHAPE_ENDPOINT,
		fetch,
		headers: {
			Authorization : `Bearer ${process.env.SVELTE_APP_TAKESHAPE_API_KEY}`
		}
	});

	const client = new ApolloClient({
  		link: link,
		cache: new InMemoryCache({
			addTypename: true
		})
	});
	setClient(client);

	const collectionList = query(gql`
		query  {
			getCollectionList{
				items{
					_id
					title
					products{
						name
						price
					}
				}
			}
		}
	`)
</script>

{#if $collectionList.loading}
  Loading...
{:else if $collectionList.error}
  Error: {$collectionList.error.message}
{:else}
  {#each $collectionList.data.getCollectionList.items as collection}
	<h2>{collection.title}</h2>
	<ul>
		{#each collection.products as product}
			<li>{product.name} : {product.price}</li>
		{/each}
	</ul>
  {/each}
{/if}