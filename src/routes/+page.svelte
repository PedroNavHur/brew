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
          Keep track of the beans you love with Brew Inventory. Add, edit, and
          delete your favorite beans.
        </p>
        <!-- Open the modal using ID.showModal() method -->
        <button class="btn btn-primary" on:click={handleModal}>Get Started</button>
        <dialog
          bind:this={dialog}
          id="login_modal"
          class="modal modal-bottom sm:modal-middle"
        >
          <div class="modal-box">
            <h3 class="text-lg font-bold">Hi! Sign in to keep track of your grounds!</h3>
            <p class="py-4">Press ESC key or click the button below to close</p>
            <div class="modal-action">
              <form method="dialog">
                <!-- if there is a button in form, it will close the modal -->
                <button class="btn">Close</button>
              </form>
            </div>
          </div>
        </dialog>
      </div>
    </div>
  </div>

  <form class="row flex-center flex" method="POST" use:enhance={handleSubmit}>
    <div class="col-6 form-widget">
      <h1 class="header">Supabase + SvelteKit</h1>
      <p class="description">Sign in via magic link with your email below</p>
      {#if form?.message !== undefined}
        <div class="success {form?.success ? '' : 'fail'}">
          {form?.message}
        </div>
      {/if}
      <div>
        <label for="email">Email address</label>
        <input
          id="email"
          name="email"
          class="inputField"
          type="email"
          placeholder="Your email"
          value={form?.email ?? ""}
        />
      </div>
      {#if form?.errors?.email}
        <span class="error flex items-center text-sm">
          {form?.errors?.email}
        </span>
      {/if}
      <div>
        <button class="button primary block">
          {loading ? "Loading" : "Send magic link"}
        </button>
      </div>
    </div>
  </form>
  <h1>
    <span class="welcome">
      <picture>
        <source srcset={welcome} type="image/webp" />
        <img src={welcome_fallback} alt="Welcome" />
      </picture>
    </span>

    to your new<br />SvelteKit app
    <button class="btn btn-primary">Click me</button>
  </h1>
  <!-- 
	<ul>
		{#each beans as bean}
			<li>{JSON.stringify(bean)}</li>
		{/each}
	</ul> -->

  <h2>
    try editing <strong>src/routes/+page.svelte</strong>
  </h2>
</section>

<style>
  section {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    flex: 0.6;
  }

  h1 {
    width: 100%;
  }

  .welcome {
    display: block;
    position: relative;
    width: 100%;
    height: 0;
    padding: 0 0 calc(100% * 495 / 2048) 0;
  }

  .welcome img {
    position: absolute;
    width: 100%;
    height: 100%;
    top: 0;
    display: block;
  }
</style>
