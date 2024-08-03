<script>
  import Header from "./Header.svelte";
  import "tailwindcss/tailwind.css";

  import { invalidate } from "$app/navigation";
  import { onMount } from "svelte";

  export let data;
  $: ({ session, supabase } = data);

  onMount(() => {
    const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
      if (newSession?.expires_at !== session?.expires_at) {
        invalidate("supabase:auth");
      }
    });

    return () => data.subscription.unsubscribe();
  });
</script>

<div class="h-svh bg-neutral-50">
  <Header />

  <main class="m-4 flex max-w-screen-md justify-center">
    <slot />
  </main>

  <footer></footer>
</div>
