<script>
	import { onMount } from 'svelte';
	import FetchData from '../../../utils/FetchData';
	import { VITE_API_AUTHORIZATION, VITE_API_KEY } from '../../../utils/env';
	import { goto } from '$app/navigation';

	export let callback;
	export let categories;
	export let idProduct;

	console.log('idProduct', idProduct);
    let successMessage = '';
	let errorMessage = '';
	let productCurrent = {};
	let dataForm = {
		name: '',
		precio: '',
		stock: '',
		descripcion: '',
		categoria: '',
		img: ''
	};
	const sendData = async () => {
		console.log(dataForm);

		const responseProduct = await FetchData(
			`https://eelkfypaxjhdnroismga.supabase.co/rest/v1/product?id=eq.${idProduct}`,
			{
				method: 'PATCH',
				headers: {
					'Content-Type': 'application/json',
					apikey: VITE_API_KEY,
					Authorization: VITE_API_AUTHORIZATION,
					Prefer: 'return=representation'
				},
				body: {
					name: dataForm.name,
					image: dataForm.img,
					id_category: dataForm.categoria,
					price: dataForm.precio,
					stock: dataForm.stock,
					description: dataForm.descripcion
				}
			}
		);

		console.log(responseProduct);

		if (responseProduct) {
			successMessage = 'Producto actualizado correctamente.';
            callback(false)
			goto('/');

			errorMessage = '';
		} else {
			const errorData = responseProduct;
			errorMessage = 'Hubo un error al actualizar el producto.';
			console.error('Error response:', errorData);
		}
	};

	const getProvider = async () => {
		const responseClients = await FetchData(
			`https://eelkfypaxjhdnroismga.supabase.co/rest/v1/product?id=eq.${idProduct}&select=*`,
			{
				headers: {
					apikey: VITE_API_KEY,
					Authorization: VITE_API_AUTHORIZATION
				}
			}
		);
		console.log('responseClients', responseClients);

		productCurrent = { ...responseClients[0] };

		dataForm = {
			name: productCurrent.name,
			precio: productCurrent.price,
			stock: productCurrent.stock,
			descripcion: productCurrent.description,
			categoria: productCurrent.id_category,
			img: productCurrent.image
		};
		console.log('productCurrent', productCurrent);
	};
	onMount(() => {
		getProvider();
	});
</script>

<div
	class="fixed left-0 top-0 flex h-full w-full items-center justify-center bg-black bg-opacity-50 py-10"
>
	<div class="max-h-full w-full max-w-xl overflow-y-auto bg-white sm:rounded-2xl">
        {#if successMessage}
			<p class="mb-4 rounded bg-green-100 p-3 text-green-800">
				{successMessage}
			</p>
		{/if}
		{#if errorMessage}
			<p class="mb-4 rounded bg-red-100 p-3 text-red-800">
				{errorMessage}
			</p>
		{/if}
		<div class="rounded border border-gray-200 p-8">
			<h1 class="text-3xl font-medium">Agregar Producto</h1>
			<form on:submit|preventDefault={sendData}>
				<div class="mt-8 space-y-6">
					<div>
						<label for="name" class="mb-3 block text-base font-medium">Nombre del Producto</label>
						<input
							bind:value={dataForm.name}
							type="text"
							name="name"
							id="name"
							placeholder="Nombre del Producto"
							class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
						/>
					</div>

					<div>
						<label for="precio" class="mb-3 block text-base font-medium">Precio</label>
						<input
							bind:value={dataForm.precio}
							type="number"
							name="precio"
							id="precio"
							placeholder="1500"
							class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
						/>
					</div>
					<div>
						<label for="stock" class="mb-3 block text-base font-medium">Stock</label>
						<input
							bind:value={dataForm.stock}
							type="number"
							name="stock"
							id="stock"
							placeholder="60"
							class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
						/>
					</div>
					<div>
						<label for="descripcion" class="mb-3 block text-base font-medium">Descripción</label>
						<textarea
							bind:value={dataForm.descripcion}
							rows="4"
							name="descripcion"
							id="descripcion"
							placeholder="Descripción del producto"
							class="w-full resize-none rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
						></textarea>
					</div>
					<div>
						<label for="categories" class="mb-3 block text-base font-medium">Categorías</label>

						<select
							bind:value={dataForm.categoria}
							class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
						>
							{#each categories as category}
								<option value={category.id}>{category.name}</option>
							{/each}
						</select>
					</div>
				</div>

				<div class="mt-8 space-x-4">
					<button
						class="rounded bg-purple-500 px-4 py-2 text-white hover:bg-purple-600 active:bg-purple-700 disabled:opacity-50"
						type="submit"
					>
						Actualizar
					</button>
					<button
						on:click={() => callback(false)}
						class="rounded border border-gray-200 bg-white px-4 py-2 text-gray-600 hover:bg-gray-100 active:bg-gray-200 disabled:opacity-50"
					>
						Cerrar
					</button>
				</div>
			</form>
		</div>
	</div>
</div>
