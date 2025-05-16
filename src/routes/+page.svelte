<script lang="ts">
	import { onMount } from 'svelte';
	import { fade, fly } from 'svelte/transition';

	const categories = ['Featured', 'Stationery', 'Accessories', 'Tech', 'Decor', 'Games'];

	const products = [
		...Array(10)
			.fill(null)
			.map((_, i) => ({
				id: i + 1,
				name: `Product ${i + 1}`,
				price: i + 1,
				category: 'Featured',
				image: `https://placehold.co/300x300/3b82f6/ffffff?text=Product ${i + 1}`,
				description: `Product ${i + 1} description.`
			})),
		...Array(10)
			.fill(null)
			.map((_, i) => ({
				id: i + 11,
				name: `Product ${i + 11}`,
				price: i + 11,
				category: 'Stationery',
				image: `https://placehold.co/300x300/3b82f6/ffffff?text=Product ${i + 11}`,
				description: `Product ${i + 11} description.`
			})),
		...Array(10)
			.fill(null)
			.map((_, i) => ({
				id: i + 21,
				name: `Product ${i + 21}`,
				price: i + 21,
				category: 'Accessories',
				image: `https://placehold.co/300x300/3b82f6/ffffff?text=Product ${i + 21}`,
				description: `Product ${i + 21} description.`
			})),
		...Array(10)
			.fill(null)
			.map((_, i) => ({
				id: i + 31,
				name: `Product ${i + 31}`,
				price: i + 31,
				category: 'Tech',
				image: `https://placehold.co/300x300/3b82f6/ffffff?text=Product ${i + 31}`,
				description: `Product ${i + 31} description.`
			})),
		...Array(10)
			.fill(null)
			.map((_, i) => ({
				id: i + 41,
				name: `Product ${i + 41}`,
				price: i + 41,
				category: 'Decor',
				image: `https://placehold.co/300x300/3b82f6/ffffff?text=Product ${i + 41}`,
				description: `Product ${i + 41} description.`
			})),
		...Array(10)
			.fill(null)
			.map((_, i) => ({
				id: i + 51,
				name: `Product ${i + 51}`,
				price: i + 51,
				category: 'Games',
				image: `https://placehold.co/300x300/3b82f6/ffffff?text=Product ${i + 51}`,
				description: `Product ${i + 51} description.`
			}))
	];

	let cart: Array<{ product: (typeof products)[0]; quantity: number }> = [];
	let cartOpen = false;
	let checkoutOpen = false;
	let confirmationOpen = false;
	let selectedCategory = 'Featured';
	let searchQuery = '';

	let customerInfo = {
		name: '',
		email: '',
		phone: '',
		address: '',
		city: '',
		postalCode: ''
	};

	let shippingCost = 0;

	$: filteredProducts = products.filter((product) => {
		const matchesCategory =
			selectedCategory === 'Featured' || product.category === selectedCategory;
		const matchesSearch = product.name.toLowerCase().includes(searchQuery.toLowerCase());
		return matchesCategory && matchesSearch;
	});

	$: cartTotal = cart.reduce((total, item) => total + item.product.price * item.quantity, 0);
	$: cartItemCount = cart.reduce((count, item) => count + item.quantity, 0);

	function addToCart(product: (typeof products)[0]) {
		const existingItem = cart.find((item) => item.product.id === product.id);

		if (existingItem) {
			cart = cart.map((item) =>
				item.product.id === product.id ? { ...item, quantity: item.quantity + 1 } : item
			);
		} else {
			cart = [...cart, { product, quantity: 1 }];
		}
	}

	function removeFromCart(productId: number) {
		cart = cart.filter((item) => item.product.id !== productId);
	}

	function updateQuantity(productId: number, newQuantity: number) {
		if (newQuantity < 1) {
			removeFromCart(productId);
			return;
		}

		cart = cart.map((item) =>
			item.product.id === productId ? { ...item, quantity: newQuantity } : item
		);
	}

	function proceedToCheckout() {
		cartOpen = false;
		checkoutOpen = true;

		shippingCost = cartTotal >= 50 ? 0 : 5.99;
	}

	function submitOrder() {
		checkoutOpen = false;
		confirmationOpen = true;
	}

	function continueShopping() {
		cartOpen = false;
		checkoutOpen = false;
		confirmationOpen = false;
	}

	function finishOrder() {
		cart = [];
		customerInfo = {
			name: '',
			email: '',
			phone: '',
			address: '',
			city: '',
			postalCode: ''
		};
		confirmationOpen = false;
	}

	onMount(() => {});
</script>

<div class="min-h-screen bg-gray-50">
	<header class="sticky top-0 z-50 bg-white shadow-md">
		<div class="container mx-auto flex items-center justify-between px-4 py-4">
			<div class="flex items-center">
				<h1 class="text-2xl font-bold text-blue-600">Loot Box</h1>
			</div>

			<div class="flex items-center space-x-4">
				<div class="relative">
					<input
						type="text"
						placeholder="Search products..."
						bind:value={searchQuery}
						class="w-64 rounded-full border border-gray-300 px-4 py-2 focus:ring-2 focus:ring-blue-500 focus:outline-none"
					/>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="absolute top-2.5 right-3 h-5 w-5 text-gray-400"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"
						/>
					</svg>
				</div>

				<button
					on:click={() => (cartOpen = true)}
					class="relative p-2 text-gray-700 transition-colors hover:text-blue-600"
				>
					<svg
						xmlns="http://www.w3.org/2000/svg"
						class="h-6 w-6"
						fill="none"
						viewBox="0 0 24 24"
						stroke="currentColor"
					>
						<path
							stroke-linecap="round"
							stroke-linejoin="round"
							stroke-width="2"
							d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"
						/>
					</svg>
					{#if cartItemCount > 0}
						<span
							class="absolute -top-1 -right-1 flex h-5 w-5 items-center justify-center rounded-full bg-blue-600 text-xs text-white"
						>
							{cartItemCount}
						</span>
					{/if}
				</button>
			</div>
		</div>
	</header>

	<div class="sticky top-16 z-40 bg-white shadow-sm">
		<div class="container mx-auto overflow-x-auto px-4 py-2">
			<div class="flex space-x-6">
				{#each categories as category}
					<button
						class="px-3 py-2 text-sm font-medium whitespace-nowrap transition-colors {selectedCategory ===
						category
							? 'border-b-2 border-blue-600 text-blue-600'
							: 'text-gray-600 hover:text-blue-600'}"
						on:click={() => (selectedCategory = category)}
					>
						{category}
					</button>
				{/each}
			</div>
		</div>
	</div>

	<main class="container mx-auto px-4 py-8">
		{#if selectedCategory === 'Featured'}
			<div class="mb-8 rounded-xl bg-gradient-to-r from-blue-500 to-indigo-600 p-8 text-white">
				<div class="max-w-2xl">
					<h2 class="mb-2 text-3xl font-bold">Welcome to Loot Box!</h2>
					<p class="mb-4 text-lg">PlaceHolder Text</p>
					<button
						class="rounded-full bg-white px-6 py-2 font-medium text-blue-600 transition-colors hover:bg-gray-100"
						on:click={() => window.scrollTo({ top: 500, behavior: 'smooth' })}
					>
						Shop Now
					</button>
				</div>
			</div>
		{/if}

		<div class="grid grid-cols-1 gap-6 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4">
			{#each filteredProducts as product (product.id)}
				<div
					class="overflow-hidden rounded-lg bg-white shadow-md transition-shadow hover:shadow-lg"
				>
					<img src={product.image} alt={product.name} class="h-48 w-full object-cover" />
					<div class="p-4">
						<h3 class="mb-1 text-lg font-semibold text-gray-800">{product.name}</h3>
						<p class="mb-3 text-sm text-gray-600">{product.description}</p>
						<div class="flex items-center justify-between">
							<span class="font-bold text-blue-600">${product.price.toFixed(2)}</span>
							<button
								on:click={() => addToCart(product)}
								class="rounded-full bg-blue-600 px-3 py-1 text-sm text-white transition-colors hover:bg-blue-700"
							>
								Add to Cart
							</button>
						</div>
					</div>
				</div>
			{/each}
		</div>

		{#if filteredProducts.length === 0}
			<div class="py-12 text-center">
				<svg
					xmlns="http://www.w3.org/2000/svg"
					class="mx-auto mb-4 h-16 w-16 text-gray-400"
					fill="none"
					viewBox="0 0 24 24"
					stroke="currentColor"
				>
					<path
						stroke-linecap="round"
						stroke-linejoin="round"
						stroke-width="2"
						d="M9.172 16.172a4 4 0 015.656 0M9 10h.01M15 10h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"
					/>
				</svg>
				<h3 class="mb-2 text-xl font-medium text-gray-700">No products found</h3>
				<p class="text-gray-500">
					Try adjusting your search or filter to find what you're looking for.
				</p>
			</div>
		{/if}
	</main>

	<footer class="bg-gray-800 py-8 text-white">
		<div class="container mx-auto px-4">
			<div class="grid grid-cols-1 gap-8 md:grid-cols-3">
				<div>
					<h3 class="mb-4 text-xl font-bold">Loot Box</h3>
				</div>
				<div>
					<h3 class="mb-4 text-lg font-semibold">Quick Links</h3>
					<ul class="space-y-2">
						<li><a href="/" class="text-gray-300 transition-colors hover:text-white">Home</a></li>
					</ul>
				</div>
				<div>
					<h3 class="mb-4 text-lg font-semibold">Contact Us</h3>
					<p class="mb-2 text-gray-300">Email: your email@lootbox.ca</p>
					<div class="mt-4 flex space-x-4">
						<a
							href="/"
							class="text-gray-300 transition-colors hover:text-white"
							aria-label="Instagram"
						>
							<svg
								xmlns="http://www.w3.org/2000/svg"
								class="h-6 w-6"
								fill="currentColor"
								viewBox="0 0 24 24"
							>
								<path
									d="M12 2.163c3.204 0 3.584.012 4.85.07 3.252.148 4.771 1.691 4.919 4.919.058 1.265.069 1.645.069 4.849 0 3.205-.012 3.584-.069 4.849-.149 3.225-1.664 4.771-4.919 4.919-1.266.058-1.644.07-4.85.07-3.204 0-3.584-.012-4.849-.07-3.26-.149-4.771-1.699-4.919-4.92-.058-1.265-.07-1.644-.07-4.849 0-3.204.013-3.583.07-4.849.149-3.227 1.664-4.771 4.919-4.919 1.266-.057 1.645-.069 4.849-.069zm0-2.163c-3.259 0-3.667.014-4.947.072-4.358.2-6.78 2.618-6.98 6.98-.059 1.281-.073 1.689-.073 4.948 0 3.259.014 3.668.072 4.948.2 4.358 2.618 6.78 6.98 6.98 1.281.058 1.689.072 4.948.072 3.259 0 3.668-.014 4.948-.072 4.354-.2 6.782-2.618 6.979-6.98.059-1.28.073-1.689.073-4.948 0-3.259-.014-3.667-.072-4.947-.196-4.354-2.617-6.78-6.979-6.98-1.281-.059-1.69-.073-4.949-.073zm0 5.838c-3.403 0-6.162 2.759-6.162 6.162s2.759 6.163 6.162 6.163 6.162-2.759 6.162-6.163c0-3.403-2.759-6.162-6.162-6.162zm0 10.162c-2.209 0-4-1.79-4-4 0-2.209 1.791-4 4-4s4 1.791 4 4c0 2.21-1.791 4-4 4zm6.406-11.845c-.796 0-1.441.645-1.441 1.44s.645 1.44 1.441 1.44c.795 0 1.439-.645 1.439-1.44s-.644-1.44-1.439-1.44z"
								/>
							</svg>
						</a>
					</div>
				</div>
			</div>
			<div class="mt-8 border-t border-gray-700 pt-6 text-center text-gray-400">
				<p>&copy; {new Date().getFullYear()} Loot Box. All rights reserved.</p>
			</div>
		</div>
	</footer>

	{#if cartOpen}
		<div class="fixed inset-0 z-50 overflow-hidden" transition:fade={{ duration: 200 }}>
			<div
				class="bg-opacity-50 absolute inset-0 transition-opacity"
				on:click={() => (cartOpen = false)}
				on:keydown={(e) => e.key === 'Escape' && (cartOpen = false)}
				tabindex="0"
				aria-label="Close"
				role="button"
				transition:fade={{ duration: 150 }}
			></div>
			<div class="fixed inset-y-0 right-0 flex max-w-full">
				<div
					class="relative w-screen max-w-md transform transition-transform duration-300 ease-in-out"
					class:translate-x-0={cartOpen}
					class:translate-x-full={!cartOpen}
					transition:fly={{ x: 300, duration: 300 }}
				>
					<div class="flex h-full flex-col overflow-y-auto bg-white shadow-xl">
						<div class="flex-1 overflow-y-auto px-4 py-6 sm:px-6">
							<div class="flex items-start justify-between">
								<h2 class="text-lg font-medium text-gray-900">Shopping Cart</h2>
								<button
									on:click={() => (cartOpen = false)}
									class="ml-3 flex h-7 items-center"
									aria-label="Close"
								>
									<svg
										xmlns="http://www.w3.org/2000/svg"
										class="h-6 w-6 text-gray-400 hover:text-gray-500"
										fill="none"
										viewBox="0 0 24 24"
										stroke="currentColor"
									>
										<path
											stroke-linecap="round"
											stroke-linejoin="round"
											stroke-width="2"
											d="M6 18L18 6M6 6l12 12"
										/>
									</svg>
								</button>
							</div>

							<div class="mt-8">
								{#if cart.length === 0}
									<div class="py-12 text-center">
										<svg
											xmlns="http://www.w3.org/2000/svg"
											class="mx-auto mb-4 h-12 w-12 text-gray-400"
											fill="none"
											viewBox="0 0 24 24"
											stroke="currentColor"
										>
											<path
												stroke-linecap="round"
												stroke-linejoin="round"
												stroke-width="2"
												d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"
											/>
										</svg>
										<h3 class="mb-2 text-lg font-medium text-gray-900">Your cart is empty</h3>
										<p class="mb-6 text-gray-500">
											Looks like you haven't added any products to your cart yet.
										</p>
										<button
											on:click={() => (cartOpen = false)}
											class="inline-flex items-center rounded-md border border-transparent bg-blue-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-blue-700"
										>
											Continue Shopping
										</button>
									</div>
								{:else}
									{#each cart as item (item.product.id)}
										<div class="mb-6 flex items-start">
											<div class="flex-shrink-0">
												<img
													src={item.product.image}
													alt={item.product.name}
													class="h-12 w-12 rounded-full"
												/>
											</div>
											<div class="ml-4 flex flex-1 justify-between">
												<div>
													<h3 class="text-sm font-medium text-gray-900">{item.product.name}</h3>
													<p class="text-sm text-gray-500">${item.product.price.toFixed(2)}</p>
												</div>
												<div class="flex">
													<button
														on:click={() => removeFromCart(item.product.id)}
														on:keydown={() => removeFromCart(item.product.id)}
														class="ml-4 flex h-7 items-center"
														aria-label="Remove"
														tabindex="0"
													>
														<svg
															xmlns="http://www.w3.org/2000/svg"
															class="h-6 w-6 text-gray-400 hover:text-gray-500"
															fill="none"
															viewBox="0 0 24 24"
															stroke="currentColor"
														>
															<path
																stroke-linecap="round"
																stroke-linejoin="round"
																stroke-width="2"
																d="M6 18L18 6M6 6l12 12"
															/>
														</svg>
													</button>
												</div>
											</div>
										</div>
									{/each}

									<div class="mt-6 flex justify-end">
										<button
											on:click={() => proceedToCheckout()}
											class="inline-flex items-center rounded-md border border-transparent bg-blue-600 px-4 py-2 text-sm font-medium text-white shadow-sm hover:bg-blue-700"
										>
											Checkout
										</button>
									</div>
								{/if}
							</div>
						</div>
					</div>
				</div>
			</div>
		</div>
	{/if}
</div>
