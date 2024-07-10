<script lang="ts">

</script>


<div class="flex items-center justify-center p-12">

    <div class="mx-auto w-full max-w-[550px]">
      <form action="" method="POST">
        
        <div class="mb-5">
          <label
            for="name"
            class="mb-3 block text-base font-medium"
          >
            Nombre del Producto
          </label>
          <input
            type="text"
            name="name"
            id="name"
            placeholder="Nombre del Producto"
            class="w-full rounded-lg border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700  focus:shadow-md"
          />
        </div>


        <div class="mb-5">
          <label
            for="precio"
            class="mb-3 block text-base font-medium "
          >
            Precio
          </label>
          <input
            type="number"
            name="precio"
            id="precio"
            placeholder="1500"
            class="w-full rounded-lg border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700  focus:shadow-md"
          />
        </div>


        <div class="mb-5">
          <label
            for="stock"
            class="mb-3 block text-base font-medium "
          >
            Stock
          </label>
          <input
            type="number"
            name="stock"
            id="stock"
            placeholder="60"
            class="w-full rounded-lg border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700  focus:shadow-md"
          />
        </div>


        <div class="mb-5">
          <label
            for="descripcion"
            class="mb-3 block text-base font-medium"
          >
            Descripcion 
          </label>
          <textarea
            rows="4"
            name="descripcion"
            id="descripcion"
            placeholder="sdsaddjlkdfsalkdsaflk"
            class="w-full resize-none rounded-lg border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700 focus:shadow-md"
          ></textarea>
        </div>
         
        <div class="mb-5">
            <label
              for="stock"
              class="mb-3 block text-base font-medium "
            >
              Cambiar Img
            </label>
            <input
              type="file"
              name="img"
              id="img"
              placeholder="60"
              class="w-full rounded-lg border border-[#e0e0e0] bg-white py-3 px-6 text-base font-medium text-[#6B7280] outline-none focus:border-yellow-700  focus:shadow-md"
            />
          </div>

        <div>
          <button
            class="hover:shadow-form rounded-lg bg-purple-600 py-3 px-8 text-base font-semibold text-white outline-none"
          >
            Actualizar
          </button>
        </div>
      </form>
    </div>
  </div>