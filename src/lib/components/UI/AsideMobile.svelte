<script lang="ts">
	import Tienda from '../imgComponents/Tienda.svelte';
	import { page } from '$app/stores';
	import Productos from '../imgComponents/Productos.svelte';
	import Pedidos from '../imgComponents/Pedidos.svelte';
	let pathname = $derived($page.url.pathname);
</script>

<style>
	@media (min-width: 770px) {
		#menuMobile {
			display: none;
		}
	}
</style>

<div class="w-full fixed bottom-0 bg-white shadow-lg py-3 " id="menuMobile">
    <div class="max-w-md mx-auto">
        <div class="flex justify-around">
            <div class="flex-1 group">
				{@render navLinkMobile({href: '/', text: 'Inicio', SvgComponent: Tienda})}
            </div>
            <div class="flex-1 group">
				{@render navLinkMobile({href: '/productos', text: 'Productos', SvgComponent: Productos})}
            </div>
            <div class="flex-1 group">
				{@render navLinkMobile({href: '/pedidos', text: 'Pedidos', SvgComponent: Pedidos})}
            </div>
        </div>
    </div>
</div>

{#snippet navLinkMobile({href, text, SvgComponent}:{href:string, text:string, SvgComponent:any})}
<a href={href} class={`${pathname === href ? "bg-purple-200 rounded-xl text-purple-700" : "bg-transparent" } flex items-center justify-center text-center font-bold pt-2 w-full text-sm text-gray-900 hover:text-purple-700 border-b-2 border-transparent `}>
	<span class="flex  items-center">
		<SvgComponent class="text-2xl mb-1 w-6 h-6"/>
		<span class="text-xs">{text}</span>
	</span>
</a>
{/snippet}
