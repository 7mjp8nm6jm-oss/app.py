# app.py
<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Calculadora de Inventario - Cocina</title>
    <!-- Usamos Tailwind CSS para darle estilo rápido -->
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body class="bg-gray-100 p-6">
    <div class="max-w-3xl mx-auto bg-white p-6 rounded-lg shadow-md">
        <h1 class="text-2xl font-bold mb-4 text-gray-800">🍽️ Calculadora de Inventario por PX</h1>

        <!-- Selector de Receta y Comensales -->
        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-6">
            <div>
                <label class="block text-gray-700 font-medium mb-2">Selecciona una Receta:</label>
                <select id="recipe-select" class="w-full border border-gray-300 p-2 rounded-md">
                    <option value="">Cargando recetas...</option>
                </select>
            </div>
            <div>
                <label class="block text-gray-700 font-medium mb-2">Número de Comensales (px):</label>
                <input type="number" id="px-input" value="1" min="1" class="w-full border border-gray-300 p-2 rounded-md">
            </div>
        </div>

        <button id="calculate-btn" class="bg-blue-600 text-white px-4 py-2 rounded-md hover:bg-blue-700 transition">
            Calcular Inventario Necesario
        </button>

        <!-- Resultados -->
        <div class="mt-8">
            <h2 class="text-xl font-semibold mb-3 text-gray-700">Inventario Requerido:</h2>
            <div class="overflow-x-auto">
                <table class="w-full text-left border-collapse">
                    <thead>
                        <tr class="bg-gray-200 text-gray-700">
                            <th class="p-2 border">Ingrediente</th>
                            <th class="p-2 border">Cantidad Necesaria</th>
                            <th class="p-2 border">Unidad</th>
                            <th class="p-2 border">Stock Actual</th>
                            <th class="p-2 border">Diferencia</th>
                        </tr>
                    </thead>
                    <tbody id="results-table-body">
                        <tr>
                            <td colspan="5" class="p-4 text-center text-gray-500">Selecciona una receta y calcula los px.</td>
                        </tr>
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- Script principal -->
    <script type="module" src="/main.js"></script>
</body>
</html>
import { createClient } from '@supabase/supabase-js'

// Inicializar Supabase usando las variables de entorno de Vite
const supabaseUrl = import.meta.env.VITE_SUPABASE_URL
const supabaseKey = import.meta.env.VITE_SUPABASE_ANON_KEY
const supabase = createClient(supabaseUrl, supabaseKey)

// Elementos del DOM
const recipeSelect = document.getElementById('recipe-select')
const pxInput = document.getElementById('px-input')
const calculateBtn = document.getElementById('calculate-btn')
const resultsTableBody = document.getElementById('results-table-body')

// 1. Cargar las recetas al iniciar la página
async function cargarRecetas() {
    const { data, error } = await supabase.from('recipes').select('id, name')
    
    if (error) {
        console.error('Error al cargar recetas:', error)
        return
    }

    recipeSelect.innerHTML = '<option value="">-- Selecciona un plato --</option>'
    data.forEach(recipe => {
        const option = document.createElement('option')
        option.value = recipe.id
        option.textContent = recipe.name
        recipeSelect.appendChild(option)
    })
}

// 2. Calcular inventario al hacer clic en el botón
async function calcularInventario() {
    const recipeId = recipeSelect.value
    const px = parseInt(pxInput.value)

    if (!recipeId) {
        alert('Por favor selecciona una receta.')
        return
    }

    // Consultamos los ingredientes de la receta uniendo la tabla relacional con los ingredientes
    const { data, error } = await supabase
        .from('recipe_ingredients')
        .select(`
            quantity_per_base_portion,
            ingredients (
                name,
                unit_of_measure,
                current_stock
            )
        `)
        .eq('recipe_id', recipeId)

    if (error) {
        console.error('Error al calcular:', error)
        return
    }

    // Limpiar tabla anterior
    resultsTableBody.innerHTML = ''

    if (data.length === 0) {
        resultsTableBody.innerHTML = `<tr><td colspan="5" class="p-4 text-center text-gray-500">Esta receta no tiene ingredientes registrados.</td></tr>`
        return
    }

    // Rellenar la tabla con el cálculo por px
    data.forEach(item => {
        const ingrediente = item.ingredients
        const cantidadNecesaria = item.quantity_per_base_portion * px
        const diferencia = ingrediente.current_stock - cantidadNecesaria

        // Definir color de alerta si falta stock
        const stockClass = diferencia < 0 ? 'text-red-600 font-bold' : 'text-green-600'

        const row = `
            <tr class="border-b">
                <td class="p-2 border">${ingrediente.name}</td>
                <td class="p-2 border font-semibold">${cantidadNecesaria.toFixed(2)}</td>
                <td class="p-2 border">${ingrediente.unit_of_measure}</td>
                <td class="p-2 border">${ingrediente.current_stock}</td>
                <td class="p-2 border ${stockClass}">${diferencia.toFixed(2)}</td>
            </tr>
        `
        resultsTableBody.innerHTML += row
    })
}

// Eventos
calculateBtn.addEventListener('click', calcularInventario)

// Inicializar la carga de recetas al abrir la app
cargarRecetas()

