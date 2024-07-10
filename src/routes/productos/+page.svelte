<script lang="ts">
	import { onMount } from 'svelte';
	import FetchData from '../../utils/FetchData';
	import { VITE_API_KEY, VITE_API_AUTHORIZATION } from '../../utils/env';
	import { goto } from '$app/navigation';

	let dataForm: any = {
		name: '',
		precio: '',
		stock: '',
		descripcion: '',
		categoria: '',
		img: ''
	};

	let categories: any[] = [];
	let successMessage = '';
	let errorMessage = '';

	const getCategories = async () => {
		try {
			const response = await FetchData(
				'https://eelkfypaxjhdnroismga.supabase.co/rest/v1/category?select=*',
				{
					headers: {
						apikey: VITE_API_KEY,
						Authorization: VITE_API_AUTHORIZATION
					}
				}
			);
			/* categories = response.body.map((category: any) => ({
        label: category.name,
        value: category.name
      })); */
			categories = response;
		} catch (error) {
			console.error('Error fetching categories:', error);
		}
	};

	onMount(() => {
		getCategories();
	});

	const sendData = async (event: Event) => {
		event.preventDefault();
		console.log(dataForm);

    const responseProduct = await FetchData(
      "https://eelkfypaxjhdnroismga.supabase.co/rest/v1/product",
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          apikey: VITE_API_KEY,
          Authorization: VITE_API_AUTHORIZATION,
          Prefer: "return=representation",
        },
        body: {
					name: dataForm.name,
					image: dataForm.img,
					id_category: dataForm.categoria,
					price: dataForm.precio,
					stock: dataForm.stock,
					description: dataForm.descripcion
				},
      }
    );


      console.log(responseProduct)

      if (responseProduct) {
				successMessage = 'Producto insertado correctamente.';
				goto('/');

				errorMessage = '';
			} else {
				const errorData = await response.json();
				errorMessage = 'Hubo un error al insertar el producto.';
				console.error('Error response:', errorData);
			}
	};

	const handleFileChange = (event: any) => {
		dataForm.img = event.target.files[0];
	};

	const handleCreateCategory = (inputValue: string) => {
		const newCategory = { label: inputValue, value: inputValue };
		categories = [...categories, newCategory];
		return newCategory;
	};
</script>

<div class="flex items-center justify-center p-12">
	<div class="mx-auto w-full max-w-[550px]">
    <div>
      <h2 class="text-2xl font-semibold leading-tight mb-9">Agrega un producto</h2>
    </div>
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

		<form on:submit|preventDefault={sendData}>
			<div class="mb-5">
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

			<div class="mb-5">
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

			<div class="mb-5">
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

			<div class="mb-5">
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

			<div class="mb-5">
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

			<div class="mb-5">
				<label for="img" class="mb-3 block text-base font-medium">Cambiar Imagen</label>
				<input
					on:change={handleFileChange}
					type="file"
					name="img"
					id="img"
					class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
				/>
			</div>

			<div>
				<button
					type="submit"
					class="hover:shadow-form rounded-lg bg-purple-600 px-8 py-3 text-base font-semibold text-white outline-none"
				>
					Crear
				</button>
			</div>
		</form>
	</div>
</div>
