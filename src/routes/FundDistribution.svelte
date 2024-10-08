<script lang="ts">
  import { onMount } from "svelte";
  import { supabase } from "../lib/supabaseClient";
  import { Connection, PublicKey, Transaction, SystemProgram } from "@solana/web3.js";
  import { Button } from "../components/ui/button";
  import { Input } from "../components/ui/input";
  import { Select } from "../components/ui/select";

  let beneficiaries = [];
  let selectedBeneficiaryId = "";
  let amount = "";
  let solanaConnection;

  onMount(async () => {
    await fetchBeneficiaries();
    initializeSolanaConnection();
  });

  function initializeSolanaConnection() {
    solanaConnection = new Connection("https://api.devnet.solana.com");
  }

  async function fetchBeneficiaries() {
    const { data, error } = await supabase
      .from("beneficiaries")
      .select("id, name, solana_address")
      .order("name");

    if (error) {
      console.error("Error fetching beneficiaries:", error);
    } else {
      beneficiaries = data;
    }
  }

  async function distributeFunds() {
    if (!selectedBeneficiaryId || !amount) return;

    const beneficiary = beneficiaries.find(b => b.id === selectedBeneficiaryId);
    if (!beneficiary || !beneficiary.solana_address) {
      console.error("Invalid beneficiary or missing Solana address");
      return;
    }

    try {
      // This is a simplified example. In a real-world scenario, you'd need to handle wallet connection and signing.
      const fromWallet = null; // Replace with actual wallet
      const toWallet = new PublicKey(beneficiary.solana_address);

      const transaction = new Transaction().add(
        SystemProgram.transfer({
          fromPubkey: fromWallet.publicKey,
          toPubkey: toWallet,
          lamports: amount * 1000000000 // Convert SOL to lamports
        })
      );

      // Sign and send the transaction
      // const signature = await sendAndConfirmTransaction(solanaConnection, transaction, [fromWallet]);
      console.log("Transaction sent: ", signature);

      // Record the transaction in Supabase
      const { data, error } = await supabase
        .from("transactions")
        .insert([{
          beneficiary_id: selectedBeneficiaryId,
          amount: amount,
          transaction_signature: signature
        }]);

      if (error) {
        console.error("Error recording transaction:", error);
      } else {
        console.log("Transaction recorded successfully");
      }

    } catch (error) {
      console.error("Error distributing funds:", error);
    }
  }
</script>

<div class="space-y-6">
  <h1 class="text-3xl font-bold">Fund Distribution</h1>

  <div class="flex space-x-2">
    <Select bind:value={selectedBeneficiaryId}>
      <option value="">Select a beneficiary</option>
      {#each beneficiaries as beneficiary}
        <option value={beneficiary.id}>{beneficiary.name}</option>
      {/each}
    </Select>
    <Input
      type="number"
      placeholder="Amount in SOL"
      bind:value={amount}
    />
    <Button on:click={distributeFunds}>Distribute Funds</Button>
  </div>

  <!-- Add a transaction history component here -->
</div>