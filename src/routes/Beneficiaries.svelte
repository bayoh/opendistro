<script lang="ts">
  import { onMount } from "svelte";
  import { supabase } from "../lib/supabaseClient";
  import { Button } from "../components/ui/button";
  import { Input } from "../components/ui/input";
  import { Select } from "../components/ui/select";

  let beneficiaries = [];
  let projects = [];
  let newBeneficiaryName = "";
  let selectedProjectId = "";

  onMount(async () => {
    await fetchBeneficiaries();
    await fetchProjects();
  });

  async function fetchBeneficiaries() {
    const { data, error } = await supabase
      .from("beneficiaries")
      .select("*, projects(name)")
      .order("created_at", { ascending: false });

    if (error) {
      console.error("Error fetching beneficiaries:", error);
    } else {
      beneficiaries = data;
    }
  }

  async function fetchProjects() {
    const { data, error } = await supabase
      .from("projects")
      .select("id, name")
      .order("name");

    if (error) {
      console.error("Error fetching projects:", error);
    } else {
      projects = data;
    }
  }

  async function addBeneficiary() {
    if (!newBeneficiaryName.trim() || !selectedProjectId) return;

    const { data, error } = await supabase
      .from("beneficiaries")
      .insert([{ name: newBeneficiaryName, project_id: selectedProjectId }])
      .select();

    if (error) {
      console.error("Error adding beneficiary:", error);
    } else {
      newBeneficiaryName = "";
      selectedProjectId = "";
      await fetchBeneficiaries();
    }
  }
</script>

<div class="space-y-6">
  <h1 class="text-3xl font-bold">Beneficiaries</h1>

  <div class="flex space-x-2">
    <Input
      type="text"
      placeholder="Beneficiary name"
      bind:value={newBeneficiaryName}
    />
    <Select bind:value={selectedProjectId}>
      <option value="">Select a project</option>
      {#each projects as project}
        <option value={project.id}>{project.name}</option>
      {/each}
    </Select>
    <Button on:click={addBeneficiary}>Add Beneficiary</Button>
  </div>

  <div class="grid gap-4 md:grid-cols-2 lg:grid-cols-3">
    {#each beneficiaries as beneficiary}
      <div class="bg-white p-4 rounded shadow">
        <h2 class="text-xl font-semibold">{beneficiary.name}</h2>
        <p class="text-gray-600">Project: {beneficiary.projects.name}</p>
      </div>
    {/each}
  </div>
</div>