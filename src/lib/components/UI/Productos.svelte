<script lang="ts">
    import { onMount } from 'svelte';

    export let datos: any[] = [];
    export let loading = true;
    export let error: string | null = null;

    let itemsPerPage = 3;
    let stockFilter: 'Todos' | 'En stock' | 'Sin stock' = 'Todos';
    let searchQuery = '';
    let currentPage = 1;

    onMount(async () => {
    try {
        const response = await fetch('/productos.json');
        if (!response.ok) {
            throw new Error('Error al obtener los datos');
        }
        datos = await response.json();
        console.log(datos); // Verificar datos cargados

        // Establecer el valor por defecto del select de cantidad de productos
        itemsPerPage = 3; // Aquí estableces el valor por defecto deseado

    } catch (err) {
        error = err.message;
    } finally {
        loading = false;
    }
});

    // Filtrar y paginar datos
    function getFilteredAndPaginatedData() {
        const filteredData = datos
            .filter((producto) => {
                if (stockFilter === 'En stock') {
                    return producto.stock > 0;
                } else if (stockFilter === 'Sin stock') {
                    return producto.stock === 0;
                }
                return true;
            })
            .filter((producto) => {
                return producto.name.toLowerCase().includes(searchQuery.toLowerCase());
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
            <h2 class="text-2xl font-semibold leading-tight">Productos</h2>
        </div>

        <div class="my-2 flex sm:flex-row flex-col">
            <div class="flex flex-row mb-1 sm:mb-0 border-r-2 rounded-lg">
                <div class="relative">
                    <select
                        class="h-full rounded-l-lg border block appearance-none w-full bg-white border-purple-700 text-gray-700 py-2 px-4 pr-8 leading-tight focus:outline-none focus:bg-white focus:border-yellow-700"
                        bind:value={itemsPerPage}>
                        <option value="3">3</option>
                        <option value="5">5</option>
                        <option value="10">10</option>
                    </select>
                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-purple-700">
                        <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                            <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z" />
                        </svg>
                    </div>
                </div>
                <div class="relative">
                    <select
                        class="h-full rounded-r border-t sm:rounded-r-none sm:border-r-0 border-r border-b block appearance-none w-full bg-white border-purple-700 text-gray-700 py-2 px-4 pr-8 leading-tight focus:outline-none focus:border-l focus:border-r focus:bg-white focus:border-yellow-700"
                        bind:value={stockFilter}>
                        <option value="Todos">Todos</option>
                        <option value="En stock">En stock</option>
                        <option value="Sin stock">Sin stock</option>
                    </select>
                    <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
                        <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20">
                            <path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z" />
                        </svg>
                    </div>
                </div>
            </div>
            <div class="block relative">
                <span class="h-full absolute inset-y-0 left-0 flex items-center pl-2">
                    <svg viewBox="0 0 24 24" class="h-4 w-4 fill-current text-gray-500">
                        <path d="M10 4a6 6 0 100 12 6 6 0 000-12zm-8 6a8 8 0 1114.32 4.906l5.387 5.387a1 1 0 01-1.414 1.414l-5.387-5.387A8 8 0 012 10z"></path>
                    </svg>
                </span>
                <input
                    placeholder="Buscar"
                    class="appearance-none rounded-r-lg rounded-l sm:rounded-l-none border border-purple-700 border-b block pl-8 pr-6 py-2 w-full bg-white text-sm placeholder-gray-400 text-gray-700 focus:bg-white focus:placeholder-yellow-700 focus:text-gray-700 focus:outline-none"
                    bind:value={searchQuery} />
            </div>
        </div>

        {#if loading}
            <p>Cargando...</p>
        {:else if error}
            <p>Error: {error}</p>
        {:else}
            <div class="-mx-4 sm:-mx-8 px-4 sm:px-8 py-4 overflow-x-auto">
                <div class="inline-block min-w-full shadow rounded-lg overflow-hidden">
                    <table class="min-w-full leading-normal">
                        <thead>
                            <tr>
                                <th class="px-5 py-3 border-b-2 border-yellow-200 bg-yellow-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                                    Nombre
                                </th>
                                <th class="px-5 py-3 border-b-2 border-yellow-200 bg-yellow-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                                    Precio
                                </th>
                                <th class="px-5 py-3 border-b-2 border-yellow-200 bg-yellow-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                                    Stock
                                </th>
                                <th class="px-5 py-3 border-b-2 border-yellow-200 bg-yellow-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                                    Fecha
                                </th>
                                <th class="px-5 py-3 border-b-2 border-yellow-200 bg-yellow-100 text-left text-xs font-semibold text-gray-600 uppercase tracking-wider">
                                    Opciones
                                </th>
                            </tr>
                        </thead>
                        <tbody>
                            {#each getFilteredAndPaginatedData().paginatedData as item}
                                {@render tableTemplate({url: item.image, img: item.image, titulo: item.name, fecha: item.fecha, stock: item.stock, precio: item.price })}
                            {/each}
                        </tbody>
                    </table>
                    <div class="px-5 py-5 bg-white border-t flex flex-col xs:flex-row items-center xs:justify-between">
                        <span class="text-xs xs:text-sm text-gray-900">
                            Mostrando {Math.min((currentPage - 1) * itemsPerPage + 1, getFilteredAndPaginatedData().filteredData.length)} a {Math.min(currentPage * itemsPerPage, getFilteredAndPaginatedData().filteredData.length)} de {getFilteredAndPaginatedData().filteredData.length} Productos
                        </span>
                        <div class="inline-flex mt-2 xs:mt-0">
                            <button class="text-sm bg-yellow-300 hover:bg-yellow-400 text-gray-800 font-semibold py-2 px-4 rounded-l" on:click={() => changePage(currentPage - 1)}>
                                Anterior
                            </button>
                            <button class="text-sm bg-yellow-300 hover:bg-yellow-400 text-gray-800 font-semibold py-2 px-4 rounded-r" on:click={() => changePage(currentPage + 1)}>
                                Siguiente
                            </button>
                        </div>
                    </div>
                </div>
            </div>
        {/if}
    </div>
</div>

{#snippet tableTemplate({url, img, titulo, fecha, stock, precio}:{url:string, img:string, titulo:string, fecha: string, stock:number, precio:number})}
    <tr>
        <td class={`${stock === 0 ? "bg-yellow-50" : ""} px-5 py-5 border-b border-gray-200 bg-white text-sm`}>
            <div class="flex items-center">
                <div class="flex-shrink-0 w-10 h-10">
                    <img class="w-full h-full rounded-sm" src={img} alt={titulo} />
                </div>
                <div class="ml-3">
                    <p class="text-gray-900 whitespace-no-wrap">
                        {titulo}
                    </p>
                </div>
            </div>
        </td>
        <td class={`${stock === 0 ? "bg-yellow-50" : ""} px-5 py-5 border-b border-gray-200 bg-white text-sm`}>
            <p class="text-gray-900 whitespace-no-wrap">{precio}</p>
        </td>
        <td class={`${stock === 0 ? "bg-yellow-50" : ""} px-5 py-5 border-b border-gray-200 bg-white text-sm`}>
            <p class="text-gray-900 whitespace-no-wrap">{stock}</p>
        </td>
        <td class={`${stock === 0 ? "bg-yellow-50" : ""} px-5 py-5 border-b border-gray-200 bg-white text-sm`}>
            <p class="text-gray-900 whitespace-no-wrap">{fecha}</p>
        </td>
        <td class={`${stock === 0 ? "bg-yellow-50" : ""} px-5 py-5 border-b border-gray-200 bg-white text-sm`}>
            <a href={url} class="underline text-yellow-700 ml-5">Editar</a>
            <a href={url} class="underline text-red-700">Eliminar</a>
        </td>
    </tr>
{/snippet}
