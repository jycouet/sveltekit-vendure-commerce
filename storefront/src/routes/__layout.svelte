<script lang="ts" context="module">
  import Cart from '$lib/components/cart.svelte'
  import Footer from '$lib/components/footer.svelte'
  import Hero from '$lib/components/hero.svelte'
  import Navbar from '$lib/components/navbar.svelte'
  import PageTransition from '$lib/components/page-transition.svelte'
  import { KQL_GetCollections } from '$lib/graphql/_kitql/graphqlStores'
  import type { Load } from '@sveltejs/kit'
  import { onMount } from 'svelte'
  import '../app.css'
  import { userLocale } from '../stores/locale'

  export const load: Load = async ({ fetch, url, session }) => {
    await KQL_GetCollections.queryLoad({ fetch })
    console.log('=====================')
    console.log('layout', session)
    console.log('=====================')
    return { props: { key: url.pathname } }
  }
</script>

<script lang="ts">
  export let key: string

  $: collections =
    $KQL_GetCollections.data?.collections.items.filter(
      item => item?.parent?.name === '__root_collection__'
    ) || []

  onMount(() => {
    userLocale.set(navigator.languages[0] as any)
  })
</script>

<PageTransition refresh={key}>
  <Cart {key} />
  <Navbar {collections} />
  <Hero {key} />
  <main class="container max-w-6xl mx-auto px-4 mb-20">
    <slot />
  </main>
  <Footer />
</PageTransition>
