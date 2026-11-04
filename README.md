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

