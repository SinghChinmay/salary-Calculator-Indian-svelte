<script lang="ts">
	// monthly salary calulation from LPA
	type TaxSlab = {
		lowerLimit: number;
		upperLimit: number;
		taxRate: number;
	};

	const taxSlabs: TaxSlab[] = [
		{ lowerLimit: 0, upperLimit: 300000, taxRate: 0 },
		{ lowerLimit: 300001, upperLimit: 600000, taxRate: 0.05 },
		{ lowerLimit: 600001, upperLimit: 900000, taxRate: 0.1 },
		{ lowerLimit: 900001, upperLimit: 1200000, taxRate: 0.15 },
		{ lowerLimit: 1200001, upperLimit: 1500000, taxRate: 0.2 },
		{ lowerLimit: 1500001, upperLimit: Infinity, taxRate: 0.3 }
	];

	let tax = '';
	let monthlyTax = '';

	// when tax value changes update the tax value with indian currency format
	$: tax = new Intl.NumberFormat('en-IN', {
		style: 'currency',
		currency: 'INR'
	}).format(Number(tax));

	$: monthlyTax = new Intl.NumberFormat('en-IN', {
		style: 'currency',
		currency: 'INR'
	}).format(Number(monthlyTax));

	function calculateIncomeTax(annualIncome: number, LPA = false) {
		let calcTax = 0;

		if (LPA) annualIncome = annualIncome * 100000;

		for (const slab of taxSlabs) {
			if (annualIncome > slab.lowerLimit) {
				const taxableAmount = Math.min(annualIncome, slab.upperLimit) - slab.lowerLimit;
				calcTax += taxableAmount * slab.taxRate;
			} else {
				break;
			}
		}
		monthlyTax = Math.round(calcTax / 12).toString();
		tax = Math.round(calcTax).toString();
	}

	let salaryLPA = 0;

	// keep checking salaryLPA if it is negative or not a number then set it to 0
	$: if (salaryLPA < 0 || isNaN(salaryLPA)) salaryLPA = 0;
</script>

<svelte:head>
	<meta charset="UTF-8" />
	<meta name="viewport" content="width=device-width, initial-scale=1.0" />
	<title>Income Tax Calculator</title>
</svelte:head>

<div class="container">
	<h1 class="title">Income Tax Calculator (new Regime)</h1>
	<p class="instruction">Enter your annual income in LPA</p>
	<input class="input-field" bind:value={salaryLPA} />
	<!-- submit button -->
	<button class="submit-button" on:click={() => calculateIncomeTax(salaryLPA, true)}
		>Calculate</button
	>
	<p class="result">Income Tax: {tax}</p>
	<p class="result">Per Month Tax: {monthlyTax}</p>
</div>

<style>
	.container {
		background-color: white;
		border-radius: 8px;
		padding: 40px;
		box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
		text-align: center;
		width: 400px;
	}

	.title {
		font-size: 32px;
		color: #333;
		margin-bottom: 20px;
	}

	.instruction {
		font-size: 16px;
		color: #777;
	}

	.input-field {
		width: 10%;
		padding: 10px;
		margin: 10px 0;
		border: 1px solid #ccc;
		border-radius: 4px;
		font-size: 14px;
	}

	.submit-button {
		background-color: #4caf50;
		border: none;
		color: white;
		padding: 10px 20px;
		text-align: center;
		font-size: 16px;
		cursor: pointer;
		border-radius: 4px;
		display: inline-block;
	}

	.submit-button:hover {
		background-color: #45a049;
	}

	.result {
		font-size: 18px;
		color: #333;
		margin-top: 20px;
	}
</style>
