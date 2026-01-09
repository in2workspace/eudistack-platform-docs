# ALTIA Styles para GitHub Pages

Estilos CSS corporativos de ALTIA extraídos de la plantilla oficial de documentos Word (`F_24_05_Plantilla_documento_word.dotx`).

## 🎨 Características

- **Variables CSS** para todos los colores corporativos
- **Tipografía** configurada con Verdana
- **Encabezados h1-h6** con los colores y tamaños exactos de la plantilla
- **Tablas** con estilo corporativo
- **Componentes** adicionales: botones, alertas, cards
- **Utilidades** para colores, espaciado y alineación
- **Responsive** adaptado para móviles
- **Print styles** optimizados para impresión

## 🚀 Uso Rápido

### Opción 1: Enlace directo

```html
<link rel="stylesheet" href="css/altia-styles.css">
```

### Opción 2: Con Jekyll (GitHub Pages)

Añade el archivo a tu carpeta `assets/css/` y referéncialo en tu layout:

```html
<link rel="stylesheet" href="{{ '/assets/css/altia-styles.css' | relative_url }}">
```

## 🎨 Paleta de Colores

| Color | Variable CSS | Hex | Uso |
|-------|--------------|-----|-----|
| Azul Principal | `--altia-primary` | `#184BFF` | Títulos H1, enlaces, botones primarios |
| Azul Oscuro | `--altia-primary-dark` | `#001E8C` | Títulos H2 |
| Verde Neón | `--altia-secondary` | `#00FF94` | Acentos, botones secundarios |
| Texto | `--altia-text` | `#2E2D2C` | Texto principal |
| Azul Marino | `--altia-accent3` | `#132153` | Títulos H3-H4, cabeceras de tabla |
| Azul Medio | `--altia-accent4` | `#1D3EB6` | Títulos H5-H6 |
| Azul Claro | `--altia-accent5` | `#6DA4FF` | Enlaces visitados, alertas info |
| Coral | `--altia-accent6` | `#EF7B73` | Alertas de advertencia |

## 📝 Tipografía

### Fuente Base

- **Familia**: Verdana, Geneva, sans-serif
- **Tamaño base**: 10pt
- **Line-height**: 1.5

### Encabezados

| Nivel | Tamaño | Color |
| ----- | ------ | ----- |
| H1 | 21pt | `#184BFF` |
| H2 | 18pt | `#001E8C` |
| H3 | 16pt | `#132153` |
| H4 | 14pt | `#132153` |
| H5 | 12pt | `#1D3EB6` |
| H6 | 11pt | `#1D3EB6` |

## 🧩 Componentes

### Botones

```html
<a href="#" class="btn btn-primary">Primary</a>
<a href="#" class="btn btn-secondary">Secondary</a>
<a href="#" class="btn btn-outline">Outline</a>
```

### Alertas

```html
<div class="alert alert-primary">Mensaje informativo</div>
<div class="alert alert-success">Mensaje de éxito</div>
<div class="alert alert-warning">Mensaje de advertencia</div>
<div class="alert alert-info">Nota informativa</div>
```

### Cards

```html
<div class="card">
  <div class="card-header">Título</div>
  <p>Contenido de la card...</p>
</div>
```

### Tablas

```html
<table>
  <thead>
    <tr>
      <th>Columna 1</th>
      <th>Columna 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Dato 1</td>
      <td>Dato 2</td>
    </tr>
  </tbody>
</table>
```

## 🛠 Utilidades

### Colores de texto

- `.text-primary` - Azul principal
- `.text-secondary` - Verde neón
- `.text-dark` - Gris oscuro
- `.text-muted` - Gris medio
- `.text-white` - Blanco

### Colores de fondo

- `.bg-primary` - Azul principal
- `.bg-secondary` - Verde neón
- `.bg-dark` - Azul marino oscuro
- `.bg-light` - Gris claro

### Alineación

- `.text-left`
- `.text-center`
- `.text-right`

### Márgenes

- `.mt-0` a `.mt-4` - Margin top
- `.mb-0` a `.mb-4` - Margin bottom

## 📂 Estructura de Archivos

```text
altia-styles/
├── css/
│   └── altia-styles.css    # Hoja de estilos principal
├── scss/
│   └── altia-styles.scss   # Versión SCSS (opcional)
├── index.html              # Página de demostración
└── README.md               # Esta documentación
```

## 🔧 Personalización

Puedes sobrescribir las variables CSS en tu propio archivo:

```css
:root {
  --altia-primary: #TuColorPersonalizado;
}
```

## 📱 Responsive

Los estilos incluyen breakpoints para pantallas pequeñas (< 768px):

- Tamaños de fuente reducidos proporcionalmente
- Padding ajustado en contenedores
- Tablas con fuente más pequeña

## 🖨 Impresión

Los estilos de impresión eliminan colores de fondo y ajustan los enlaces para mejor legibilidad en papel.

---

Generado a partir de la plantilla oficial ALTIA `F_24_05_Plantilla_documento_word.dotx`
