<script lang="ts">
  import { onMount } from "svelte";
  import { supabase } from "../lib/supabaseClient";
  import { Button } from "../components/ui/button";
  import { Input } from "../components/ui/input";

  let projects = [];
  let newProjectName = "";

  onMount(async () => {
    await fetchProjects();
  });

  async function fetchProjects() {
    const { data, error } = await supabase
      .from("projects")
      .select("*")
      .order("created_at", { ascending: false });

    if (error) {
      console.error("Error fetching projects:", error);
    } else {
      projects = data;
    }
  }

  async function createProject() {
    if (!newProjectName.trim()) return;

    const { data, error } = await supabase
      .from("projects")
      .insert([{ name: newProjectName }])
      .select();

    if (error) {
      console.error("Error creating project:", error);
    } else {
      newProjectName = "";
      await fetchProjects();
    }
  }
</script>

<div class="space-y-6">
  <h1 class="text-3xl font-bold">Projects</h1>

  <div class="flex space-x-2">
    <Input
      type="text"
      placeholder="New project name"
      bind:value={newProjectName}
    />
    <Button on:click={createProject}>Create Project</Button>
  </div>

  <div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
    {#each projects as project}
      <div class="bg-white p-4 rounded shadow">
        <h2 class="text-xl font-semibold">{project.name}</h2>
        <p class="text-gray-600">Created: {new Date(project.created_at).toLocaleDateString()}</p>
      </div>
    {/each}
  </div>
</div>