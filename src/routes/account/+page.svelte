<!-- src/routes/account/+page.svelte -->
<script lang="ts">
  import { enhance } from "$app/forms";
  import type { SubmitFunction } from "@sveltejs/kit";

  export let data;
  export let form;

  let { session, supabase, profile } = data;
  $: ({ session, supabase, profile } = data);

  $: ({ beans } = data);

  let profileForm: HTMLFormElement;
  let loading = false;
  let fullName: string = profile?.full_name ?? "";
  let username: string = profile?.username ?? "";
  let website: string = profile?.website ?? "";
  let avatarUrl: string = profile?.avatar_url ?? "";

  const handleSubmit: SubmitFunction = () => {
    loading = true;
    return async () => {
      loading = false;
    };
  };

  const handleSignOut: SubmitFunction = () => {
    loading = true;
    return async ({ update }) => {
      loading = false;
      update();
    };
  };

  async function addBeans() {
    const bean = {
      name: "Coffee Bean",
      roaster: "Roaster",
      roast_date: new Date(),
      roast_type: 1,
      blend: false,
      decaf: false,
      weight: 340,
      email: session.user.email,
    };
    const { data, error } = await supabase.from("beans").insert([bean]);
    if (error) console.error("error", error);

    // Update local data to trigger a re-render
    const beanWithID = { ...bean, id: crypto.randomUUID() };
    beans = [...(beans || []), beanWithID];
    console.log("data", data);
  }

  async function removeBean(beanId: number) {
    const { data, error } = await supabase
      .from("beans")
      .delete()
      .eq("id", beanId);
    if (error) console.error("error", error);
    // Update local data to trigger a re-render
    beans = beans?.filter((bean) => bean.id !== beanId) || [];
    console.log("data", data);
  }

  const getRoastType: (roastType: number) => string = (roastType) => {
    switch (roastType) {
      case 1:
        return "Filter";
      case 2:
        return "Espresso";
      case 3:
        return "Omni";
      default:
        return "Unknown";
    }
  };

  const getReadableDate: (date: string) => string = (date) => {
    return new Date(date).toLocaleDateString("en-US", {
      year: "numeric",
      month: "long",
      day: "numeric",
    });
  };
</script>

<div class="form-widget">
  <form
    class="form-widget"
    method="post"
    action="?/update"
    use:enhance={handleSubmit}
    bind:this={profileForm}
  >
    <div>
      <label for="email">Email</label>
      <input id="email" type="text" value={session.user.email} disabled />
    </div>

    <div>
      <label for="fullName">Full Name</label>
      <input
        id="fullName"
        name="fullName"
        type="text"
        value={form?.fullName ?? fullName}
      />
    </div>

    <div>
      <label for="username">Username</label>
      <input
        id="username"
        name="username"
        type="text"
        value={form?.username ?? username}
      />
    </div>

    <div>
      <label for="website">Website</label>
      <input
        id="website"
        name="website"
        type="url"
        value={form?.website ?? website}
      />
    </div>

    <div>
      <input
        type="submit"
        class="button primary block"
        value={loading ? "Loading..." : "Update"}
        disabled={loading}
      />
    </div>
  </form>

  <button on:click={addBeans} class="btn btn-primary">Add Coffee</button>

  <form method="post" action="?/signout" use:enhance={handleSignOut}>
    <div>
      <button class="button block" disabled={loading}>Sign Out</button>
    </div>
  </form>

  <h2>Beans</h2>
  <ul></ul>
  {#if beans === null}
    <p>Add some coffee beans</p>
  {:else}
    <div class="card h-80 overflow-x-auto bg-base-100 shadow-xl">
      <table class="table table-pin-rows">
        <!-- head -->
        <thead>
          <tr>
            <th>Beans</th>
            <th>Details</th>
            <th>Weight</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          {#each beans as bean}
            <!-- row 1 -->
            <tr>
              <td>
                <div class="flex items-center gap-3">
                  <div class="avatar">
                    <div class="mask mask-squircle h-12 w-12">
                      <img
                        src="https://i1.pickpik.com/photos/281/753/279/arabica-aromatic-beverage-black-coffee-preview.jpg"
                        alt="Avatar Tailwind CSS Component"
                      />
                    </div>
                  </div>
                  <div>
                    <div class="font-bold">{bean.name} {bean.id}</div>
                    <div class="text-sm opacity-50">{bean.roaster}</div>
                  </div>
                </div>
              </td>
              <td>
                Roasted on {getReadableDate(bean.roast_date)}
                <br />
                <span class="badge badge-ghost badge-sm"
                  >{getRoastType(bean.roast_type)}</span
                >
                {#if bean.blend}
                  <span class="badge badge-ghost badge-sm">Blend</span>
                {/if}
                {#if bean.decaf}
                  <span class="badge badge-ghost badge-sm">Decaf</span>
                {/if}
              </td>
              <td>{bean.weight} grams</td>
              <th class="w-1">
                <button
                  on:click={() => removeBean(bean.id)}
                  class="btn btn-circle btn-ghost btn-sm">✕</button
                >
              </th>
            </tr>
          {/each}
        </tbody>
      </table>
    </div>
  {/if}
</div>
