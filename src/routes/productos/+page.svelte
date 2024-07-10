<script lang="ts">
	import { onMount } from 'svelte';
	import { goto } from '@sveltejs/kit';
	import FetchData from '../../utils/FetchData';
	import { VITE_API_AUTHORIZATION, VITE_API_KEY } from '../../utils/env';

	let dataForm: any = {
		name: '',
		precio: '',
		stock: '',
		descripcion: '',
		category: '',
		img: ''
	};

	let categories: any[] = [];
	let selectedCategory: string = '';

	const getCategories = async () => {
		try {
			const response = await FetchData(
				'https://eelkfypaxjhdnroismga.supabase.co/rest/v1/categories',
				{
					method: 'GET',
					headers: {
						apikey: VITE_API_KEY,
						Authorization: VITE_API_AUTHORIZATION
					}
				}
			);
			categories = await response.json();
		} catch (error) {
			console.error('Error fetching categories:', error);
		}
	};

	onMount(() => {
		getCategories();
	});

	const sendData = async (event: Event) => {
		event.preventDefault();

		try {
			const formData = new FormData();
			formData.append('name', dataForm.name);
			formData.append('description', dataForm.descripcion);
			formData.append('price', dataForm.precio);
			formData.append('stock', dataForm.stock);
			formData.append('category', selectedCategory);
			formData.append('image', dataForm.img);

			const response = await fetch(
				'https://eelkfypaxjhdnroismga.supabase.co/rest/v1/product',
				{
					method: 'POST',
					headers: {
						apikey: VITE_API_KEY,
						Authorization: VITE_API_AUTHORIZATION,
					},
					body: formData
				}
			);

			if (response.ok) {
				goto('/');
			} else {
				const errorData = await response.json();
				console.error('Error response:', errorData);
			}
		} catch (error) {
			console.error('Error sending data:', error);
		}
	};

	const handleFileChange = (event: any) => {
		dataForm.img = event.target.files[0];
	};
</script>


<div class="flex items-center justify-center p-12">
	<div class="mx-auto w-full max-w-[550px]">
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
					placeholder="Descripción del Producto"
					class="w-full resize-none rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
				></textarea>
			</div>

			<div class="mb-5">
				<label for="category" class="mb-3 block text-base font-medium">Categoría</label>
				<select
					name="category"
					id="category"
					class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
					bind:value={selectedCategory}
				>
					{#each categories as category}
						<option value={category.name}>{category.name}</option>
					{/each}
				</select>
			</div>

			<div class="mb-5">
				<label for="img" class="mb-3 block text-base font-medium">Imagen</label>
				<input
					type="file"
					name="img"
					id="img"
					placeholder="Subir Imagen"
					class="w-full rounded-lg border border-[#e0e0e0] bg-white px-6 py-3 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
					on:change={handleFileChange}
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
