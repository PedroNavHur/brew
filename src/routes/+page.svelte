<script lang="ts">
  import welcome from "$lib/images/svelte-welcome.webp";
  import welcome_fallback from "$lib/images/svelte-welcome.png";

  // export let data;
  // $: ({ beans } = data);

  import { enhance } from "$app/forms";
  import type { ActionData, SubmitFunction } from "./$types.js";

  export let form: ActionData;

  let loading = false;

  const handleSubmit: SubmitFunction = () => {
    loading = true;
    return async ({ update }) => {
      update();
      loading = false;
    };
  };

  // Show and hide modal
  let dialog: HTMLDialogElement;
  const handleModal = () => {
    dialog.showModal();
  };
</script>

<svelte:head>
  <title>Home</title>
  <meta name="description" content="Svelte demo app" />
</svelte:head>

<section>
  <div class="hero min-h-screen bg-base-100">
    <div class="hero-content text-center">
      <div class="max-w-md">
        <h1 class="text-5xl font-bold">Brew Inventory</h1>
        <p class="py-6">
          Keep track of the beans you love with Brew Inventory.
          <br />
          Add, edit, and delete your favorite beans.
        </p>

        <button class="btn btn-primary" on:click={handleModal}
          >Get Started</button
        >
        <dialog
          bind:this={dialog}
          id="login_modal"
          class="modal modal-bottom sm:modal-middle"
        >
          <div class="modal-box">
            <form method="dialog">
              <button
                class="btn btn-circle btn-ghost btn-sm absolute right-2 top-2"
                >✕</button
              >
            </form>
            <h3 class="text-lg font-bold">
              Hi! Sign in to keep track of your grounds!
            </h3>
            <p class="my-4">
              No need for password, use a magic link with your email below
            </p>
            <form method="dialog">
              <form method="POST" use:enhance={handleSubmit}>
                <div class="container">
                  <label
                    class="input input-bordered my-4 flex items-center gap-2"
                    for="email"
                    >Email
                    <input
                      id="email"
                      name="email"
                      class="grow"
                      type="email"
                      placeholder="email@site.com"
                      value={form?.email ?? ""}
                    />
                  </label>
                  {#if form?.errors?.email}
                    <p class="my-4">
                      There was an error!
                      {form?.errors?.email}
                    </p>
                  {/if}
                  {#if form?.message !== undefined}
                    {#if form?.success}
                      <div role="alert" class="alert alert-success my-4">
                        <svg
                          xmlns="http://www.w3.org/2000/svg"
                          class="h-6 w-6 shrink-0 stroke-current"
                          fill="none"
                          viewBox="0 0 24 24"
                        >
                          <path
                            stroke-linecap="round"
                            stroke-linejoin="round"
                            stroke-width="2"
                            d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"
                          />
                        </svg>
                        <span
                          >Your purchase has been confirmed! {form?.message}</span
                        >
                      </div>
                    {:else}
                      <div role="alert" class="alert alert-warning my-4">
                        <svg
                          xmlns="http://www.w3.org/2000/svg"
                          class="h-6 w-6 shrink-0 stroke-current"
                          fill="none"
                          viewBox="0 0 24 24"
                        >
                          <path
                            stroke-linecap="round"
                            stroke-linejoin="round"
                            stroke-width="2"
                            d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"
                          />
                        </svg>
                        <span>Warning: {form?.message}</span>
                      </div>
                    {/if}
                  {/if}
                  <button class="btn btn-accent my-4">
                    {loading ? "Loading" : "Send magic link"}
                  </button>
                </div>
              </form>
            </form>
          </div>
        </dialog>
      </div>
    </div>
  </div>

  <!-- 
	<ul>
		{#each beans as bean}
			<li>{JSON.stringify(bean)}</li>
		{/each}
	</ul> -->
</section>
