<script lang="ts">
	import Tienda from '../imgComponents/Tienda.svelte';
	import { page } from '$app/stores';
	import Productos  from '../imgComponents/Productos.svelte';
	import Pedidos from '../imgComponents/Pedidos.svelte';
	let pathname = $derived( $page.url.pathname );




</script>
<style>
	@media (width < 769px){
		#asideDesktop{
			display: none;
		}
	}
</style>
<aside class="fixed inset-y-0 left-0 max-h-screen w-60 bg-white shadow-md" id="asideDesktop">
	<div class="flex h-full flex-col justify-between">
		<div class="flex-grow">
			<div class="border-b px-4 py-6 text-center">
				<h1 class="text-xl font-bold leading-none">
					<span class="text-purple-700">E-Commerce</span> Platform
				</h1>
			</div>
			<div class="p-4">
				<ul class="space-y-1">
					{@render navLink({href: '/', text: 'Inicio', SvgComponent: Tienda})}
					{@render navLink({href: '/productos', text: 'Productos', SvgComponent: Productos})}
					{@render navLink({href: '/pedidos', text: 'Pedidos', SvgComponent: Pedidos})}
				</ul>
			</div>
		</div>
	</div>
</aside>

{#snippet navLink({href, text, SvgComponent}:{href:string, text:string, SvgComponent:any})}
<li>
    <a href={href}  class={` ${pathname === href ? 'flex items-center bg-purple-200 rounded-xl font-bold text-sm text-purple-900 py-3 px-4' : 'flex bg-white hover:bg-purple-50 rounded-xl font-bold text-gray-900 py-3 px-4'}`}
	>
        <SvgComponent class="w-6 h-6 mr-2"/>{text}
    </a>
</li>
{/snippet}




<!-- bg-purple-200      bg-white hover:bg-purple-50 rounded-xl  '-->
