<script lang="ts">
  import { Router, Route } from "svelte-routing";
  import Navbar from "./components/Navbar.svelte";
  import Projects from "./routes/Projects.svelte";
  import Beneficiaries from "./routes/Beneficiaries.svelte";
  import FundDistribution from "./routes/FundDistribution.svelte";
</script>

<Router>
  <div class="flex flex-col min-h-screen">
    <Navbar />
    <main class="flex-grow container mx-auto px-4 py-8">
      <Route path="/" component={Projects} />
      <Route path="/beneficiaries" component={Beneficiaries} />
      <Route path="/fund-distribution" component={FundDistribution} />
    </main>
  </div>
</Router>