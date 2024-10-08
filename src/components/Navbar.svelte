<script lang="ts">
  import { Link } from "svelte-routing";
</script>

<nav class="bg-gray-800 text-white p-4">
  <div class="container mx-auto flex justify-between items-center">
    <Link to="/" class="text-xl font-bold">Project Management</Link>
    <div class="space-x-4">
      <Link to="/" class="hover:text-gray-300">Projects</Link>
      <Link to="/beneficiaries" class="hover:text-gray-300">Beneficiaries</Link>
      <Link to="/fund-distribution" class="hover:text-gray-300">Fund Distribution</Link>
    </div>
  </div>
</nav>