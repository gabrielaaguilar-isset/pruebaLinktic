<script lang="ts">
	import { onMount } from 'svelte';
	import FetchData from '../../utils/FetchData';
	import { VITE_API_KEY, VITE_API_AUTHORIZATION } from '../../utils/env';



	export let datos: any[] = [];
	export let loading = true;
	export let error: string | null = null;
	let itemsPerPage = 3;

	let searchQuery = '';
	let currentPage = 1;

	let selectedCategory = 'Todos';


    function jsonProductos(products:any) {
    return products.map(product => product.name).join(', ');
    }
	const getClients = async () => {
		const responseClients = await FetchData(
			'https://eelkfypaxjhdnroismga.supabase.co/rest/v1/order?select=*',
			{
				headers: {
					apikey: VITE_API_KEY,
					Authorization: VITE_API_AUTHORIZATION
				}
			}
		);

            console.log("responseClients", responseClients)
            console.log("loading", loading)

		itemsPerPage = 3;
		loading = false;
        console.log("loadingOther", loading)

		datos = responseClients;
        console.log("datos", datos)


	};

	onMount(() => {
		getClients();
	});

	// Filtrar y paginar datos
	function getFilteredAndPaginatedData() {
		const filteredData = datos
			
			.filter((producto) => {
				return producto.user_email.toLowerCase().includes(searchQuery.toLowerCase()) |  producto.user_name.toLowerCase().includes(searchQuery.toLowerCase());
			})
			.filter((producto) => {
				if (selectedCategory !== 'Todos') {
					return producto.categoryName === selectedCategory;
				}
				return true;
			});
	
		const paginatedData = filteredData.slice(
			(currentPage - 1) * itemsPerPage,
			currentPage * itemsPerPage
		);

		return { filteredData, paginatedData };
	}

	
	function getTotalPages() {
		const { filteredData } = getFilteredAndPaginatedData();
		
		return Math.ceil(filteredData.length / itemsPerPage);
	}

	function changePage(newPage: number) {
		if (newPage > 0 && newPage <= getTotalPages()) {
			currentPage = newPage;
		}
	}
	
	
</script>

<div class="container mx-auto px-4 sm:px-8">
	<div class="py-8">
		<div>
			<h2 class="text-2xl font-semibold leading-tight">Pedidos</h2>
		</div>

		<div class="my-2 flex flex-col sm:flex-row">
			<div class="mb-1 flex flex-row rounded-lg border-r-2 sm:mb-0">
				<div class="relative">
					<select
						class="block h-full w-full appearance-none rounded-l-lg border border-purple-700  bg-white px-4 py-2 pr-8 leading-tight text-gray-700 focus:border-yellow-700 focus:bg-white focus:outline-none"
						bind:value={itemsPerPage}
					>
						<option value="3">3</option>
						<option value="5">5</option>
						<option value="10">10</option>
					</select>
					<div
						class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-purple-700"
					>
						<svg
							class="h-4 w-4 fill-current"
							xmlns="http://www.w3.org/2000/svg"
							viewBox="0 0 20 20"
						>
							<path
								d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"
							/>
						</svg>
					</div>
				</div>
               
			</div>
			<div class="relative block">
				<span class="absolute inset-y-0 left-0 flex h-full items-center pl-2">
					<svg viewBox="0 0 24 24" class="h-4 w-4 fill-current text-gray-500">
						<path
							d="M10 4a6 6 0 100 12 6 6 0 000-12zm-8 6a8 8 0 1114.32 4.906l5.387 5.387a1 1 0 01-1.414 1.414l-5.387-5.387A8 8 0 012 10z"
						></path>
					</svg>
				</span>
				<input
					placeholder="Buscar"
					class="block w-full appearance-none rounded-l rounded-r-lg border border-b border-purple-700 bg-white py-2 pl-8 pr-6 text-sm text-gray-700 placeholder-gray-400 focus:bg-white focus:text-gray-700 focus:placeholder-yellow-700 focus:outline-none sm:rounded-l-none"
					bind:value={searchQuery}
				/>
			</div>
		</div>

		{#if loading}
			<p>Cargando...</p>
		{:else if error}
			<p>Error: {error}</p>
		{:else}
			<div class="-mx-4 overflow-x-auto px-4 py-4 sm:-mx-8 sm:px-8">
				<div class="inline-block min-w-full overflow-hidden rounded-lg shadow">
					<table class="min-w-full leading-normal">
						<thead>
							<tr>
								<th
									class="border-b-2 border-yellow-200 bg-yellow-100 px-5 py-3 text-left text-xs font-semibold uppercase tracking-wider text-gray-600"
								>
									Usuario
								</th>
								<th
									class="border-b-2 border-yellow-200 bg-yellow-100 px-5 py-3 text-left text-xs font-semibold uppercase tracking-wider text-gray-600"
								>
									Email
								</th>
								<th
									class="border-b-2 border-yellow-200 bg-yellow-100 px-5 py-3 text-left text-xs font-semibold uppercase tracking-wider text-gray-600"
								>
									Total
								</th>
                                <th
									class="border-b-2 border-yellow-200 bg-yellow-100 px-5 py-3 text-left text-xs font-semibold uppercase tracking-wider text-gray-600"
								>
									productos
								</th>
								<th
									class="border-b-2 border-yellow-200 bg-yellow-100 px-5 py-3 text-left text-xs font-semibold uppercase tracking-wider text-gray-600"
								>
									Fecha
								</th>
								
							</tr>
						</thead>
						<tbody>
							{#each getFilteredAndPaginatedData().paginatedData as item}
								{@render tableTemplate({
									id: item.id,
									ordenDate: item.order_date,
									userName: item.user_name,
                                    userEmail: item.user_email,
									product_name: item.product_name,
                                    total: item.total_price
								})}
							{/each}
						</tbody>
					</table>
					<div
						class="xs:flex-row xs:justify-between flex flex-col items-center border-t bg-white px-5 py-5"
					>
						<span class="xs:text-sm text-xs text-gray-900">
							Mostrando {Math.min(
								(currentPage - 1) * itemsPerPage + 1,
								getFilteredAndPaginatedData().filteredData.length
							)} a {Math.min(
								currentPage * itemsPerPage,
								getFilteredAndPaginatedData().filteredData.length
							)} de {getFilteredAndPaginatedData().filteredData.length} Productos
						</span>
						<div class="xs:mt-0 mt-2 inline-flex">
							<button
								class="rounded-l bg-yellow-300 px-4 py-2 text-sm font-semibold text-gray-800 hover:bg-yellow-400"
								on:click={() => changePage(currentPage - 1)}
							>
								Anterior
							</button>
							<button
								class="rounded-r bg-yellow-300 px-4 py-2 text-sm font-semibold text-gray-800 hover:bg-yellow-400"
								on:click={() => changePage(currentPage + 1)}
							>
								Siguiente
							</button>
						</div>
					</div>
				</div>
			</div>
		{/if}
	</div>
</div>

{#snippet tableTemplate({
	userName,
    ordenDate,
    userEmail, 
    product_name,
    total,
    id
}: {
	userName:string,
    ordenDate:string,
    userEmail:string, 
    product_name: any,
    total:any,
    id:any
})}


	<tr>
		<td
			class="border-b border-gray-200 bg-white px-5 py-5 text-sm"
		>
			<div class="flex items-center">
				<div class="ml-3">
					<p class="whitespace-no-wrap text-gray-900">
						{userName}
					</p>
				</div>
			</div>
		</td>

		<td
			class="border-b border-gray-200 bg-white px-5 py-5 text-sm"
		>
			<p class="whitespace-no-wrap text-gray-900">{userEmail}</p>
		</td>

        <td
			class="border-b border-gray-200 bg-white px-5 py-5 text-sm"
		>
			<p class="whitespace-no-wrap text-gray-900">{total}</p>
		</td>
        <td
        class="border-b border-gray-200 bg-white px-5 py-5 text-sm"
    >
        <p class="whitespace-no-wrap text-gray-900">{jsonProductos(product_name)}</p>
    </td>
		<td
			class="border-b border-gray-200 bg-white px-5 py-5 text-sm"
		>
			<p class="whitespace-no-wrap text-gray-900">{ordenDate}</p>
		</td>
		
	</tr>
{/snippet}
