<script lang="ts">
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';

    type Transaction = {
        amount: number;
        date: string;
        category: string;
    };

    const transactions = writable<Transaction[]>([]);
    let amount: number;
    let date: string;
    let category: string;

    const addTransaction = async () => {
        const res = await fetch("/transactions", {
            method: "POST",
            body: JSON.stringify({ amount, date, category }),
        });
        const newTransaction = await res.json();
        transactions.update(ts => [...ts, newTransaction]);
    };

    const getTransactions = async () => {
        const response = await fetch("/transactions", {
            method: "GET",
        });
        const data = await response.json();
        transactions.set(data);
    };

    onMount(async () => {
        try {
            const registration = await navigator.serviceWorker.register("/sw.js", {
                scope: "/"
            });
            console.log("Service worker registered:", registration);
        } catch (error) {
            console.error(`Service worker registration failed: ${error}`);
        }

        await getTransactions();
    });

    $: {
        // Calculate totalCredits, totalDebits, highestCategory as before
    }
</script>

<main>
    <form id="transaction-form" on:submit|preventDefault="{addTransaction}">
        <input type="number" bind:value="{amount}" name="amount" placeholder="amount" required>
        <input type="date" bind:value="{date}" name="date" placeholder="date" required>
        <select bind:value="{category}" name="category" aria-label="Select Category" required>
            <option selected disabled value="">Select your category</option>
            <option>Bills and Utilities</option>
            <option>Charity</option>
            <option>Commute</option>
            <option>Education</option>
            <option>Entertainment</option>
            <option>Family and Pets</option>
            <option>Fees & Charges</option>
            <option>Fitness</option>
            <option>Food and Drinks</option>
            <option>Fuel</option>
            <option>Groceries</option>
            <option>Household</option>
            <option>Medical</option>
            <option>Personal Care</option>
            <option>Rent</option>
            <option>Shopping</option>
            <option>Travel</option>
        </select>
        <button type="submit">add transaction</button>
    </form>

    <section id="stats">
        <!-- Display totalCredits, totalDebits, highestCategory as before -->
    </section>

    <h1>Transactions</h1>
    <hr>
    <table class="striped">
        <thead>
            <tr>
                <th scope="col">Date</th>
                <th scope="col">Category</th>
                <th scope="col">Amount</th>
                <th scope="col">Cumulative</th>
            </tr>
        </thead>
        <tbody>
            {#each $transactions as transaction}
                <tr>
                    <td>{transaction.date}</td>
                    <td>{transaction.category}</td>
                    <td>{transaction.amount}</td>
                    <td>TODO: Calculate cumulative</td>
                </tr>
            {/each}
        </tbody>
    </table>
</main>

<style>
    /* Add your styles here */
</style>
