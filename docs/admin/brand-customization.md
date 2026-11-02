# Personalización de marca

Esta página es para quien prepara la imagen de su organización en EUDIStack: el equipo de marca, de comunicación o el responsable del proyecto. Explica qué podemos personalizar, qué material necesitamos y cómo lo ponemos en marcha.

---

## Qué se personaliza

Tus usuarios ven tu marca en las interfaces con las que trabajan:

- **La app Wallet**, donde las personas guardan y presentan sus atestaciones.
- **La página de inicio de sesión**, donde las personas se identifican con su wallet.
- **El portal de emisión**, donde una persona obtiene su atestación.
- **El portal de administración**, donde tu organización emite y gestiona atestaciones.
- **El verificador presencial**, para comprobar atestaciones en persona.
- **Los correos** que reciben tus usuarios: código de activación, atestación lista, incidencias y avisos de revocación o caducidad.

En todas ellas aplicamos tu nombre, tus colores, tu logo y tu favicon. En la app Wallet aplicamos también el icono de la app instalada y el diseño de la tarjeta de atestación.

---

## Qué nos tienes que enviar

### Logos e iconos

| Elemento | Formato | Tamaño | Para qué |
|---|---|---|---|
| Logo para fondo de color | SVG (preferido) o PNG con fondo transparente | PNG de al menos 500 px de ancho y menos de 150 KB. Formato horizontal | Barras superiores con tu color principal |
| Logo para fondo blanco | SVG o PNG con fondo transparente | Igual que el anterior | Cabeceras blancas |
| Favicon | SVG, o PNG cuadrado | 32 x 32 px y 180 x 180 px. Menos de 50 KB | Pestaña del navegador y acceso directo en móvil |
| Icono de la app | PNG cuadrado, sin transparencia | 512 x 512 px. Deja el motivo dentro del 80 % central: algunos móviles recortan el icono en círculo | Icono de la app Wallet instalada en el móvil |

Si tu logo tiene una sola versión que funciona sobre blanco y sobre color, envía solo esa. Te avisaremos si en alguna pantalla no se lee bien.

### Colores

Envía cada color en hexadecimal de seis cifras, por ejemplo `#0F2B5B`.

| Color | Obligatorio | Dónde se ve |
|---|---|---|
| Principal | Sí | Barras superiores y zonas de marca |
| Texto sobre el principal | Sí | Textos e iconos encima del color principal |
| Secundario | Sí | Detalles de marca. También botones e iconos si tu principal es blanco o casi blanco |
| Texto sobre el secundario | Sí | Textos encima del color secundario |
| Tarjeta de atestación: color inicial, color final y texto | No | Tarjeta que ve el usuario en su app Wallet. Es un degradado entre los dos colores |
| Pantallas de acceso: color inicial y color final | No | Fondo de la pantalla de inicio de sesión y de acceso a la app |

Si no envías los opcionales, los calculamos a partir del principal y el secundario.

!!! tip "Contraste"
    Las interfaces deben poder leerlas todas las personas. Comprueba cada pareja de color de fondo y color de texto con un comprobador de contraste WCAG. Pedimos al menos 4,5:1 para el texto y 3:1 para iconos, bordes y elementos gráficos. Un color principal blanco o muy claro funciona, pero entonces los botones toman el color secundario: comprueba que el secundario también contrasta con el blanco.

### Idiomas

Elige los idiomas que ofrecerán tus interfaces y cuál es el idioma por defecto.

- Español e inglés están disponibles en todas las interfaces.
- Catalán está disponible en la app Wallet, la página de inicio de sesión, el portal de administración y el verificador presencial. En el portal de emisión, no.
- Los correos se envían en un solo idioma por organización: español o inglés.

Cada persona ve las interfaces en el idioma de su navegador si tu organización lo ofrece. Si no, en el idioma por defecto.

### Textos y enlaces

| Elemento | Formato | Dónde se ve |
|---|---|---|
| Nombre de la organización | Texto. Recomendado 20 caracteres o menos | Título de las páginas, nombre de la app instalada y asunto de los correos |
| Página de soporte | Dirección `https` | Enlaces de ayuda de la app Wallet, la página de inicio de sesión y los correos |
| Correo de soporte | Dirección de correo | App Wallet y portal de emisión |
| Teléfono, horario y plazo de respuesta del soporte | Texto | Portal de emisión |
| Centro de ayuda | Dirección `https` (opcional) | App Wallet y correos |
| Aviso legal, política de privacidad, términos de uso y declaración de accesibilidad | Dirección `https` | Portal de emisión |
| Textos propios de cada paso del portal de emisión | Texto por idioma (opcional) | Portal de emisión |

### Correos

| Elemento | Formato | Tamaño |
|---|---|---|
| Logo para correo | PNG con fondo transparente | Se muestra a 54 px de alto. Envíalo a 300 px de alto como máximo |
| Imagen de cabecera (opcional) | PNG | **640 x 190 px exactos.** Algunos programas de correo repiten la imagen si mide otra cosa |
| Redes sociales (opcional) | Dirección de tu perfil en LinkedIn o GitHub | — |

### Aplicaciones que piden presentaciones

Si tus aplicaciones piden a las personas que presenten una atestación, la app Wallet muestra quién lo pide. Para cada aplicación, envíanos su nombre, su logo, su web y, si las tiene, su política de privacidad y sus términos de uso.

### Cabecera y pie de la página de inicio de sesión (opcional)

Puedes integrar la cabecera y el pie de tu web en la página de inicio de sesión. Envíanos el HTML. Admitimos estructura, enlaces `https`, imágenes, texto y SVG. No admitimos scripts, hojas de estilo, formularios ni iframes: los quitamos al cargar la página.

---

## Qué no se puede personalizar hoy

- **La tipografía.** Todas las interfaces usan la tipografía del producto.
- **Los colores de estado.** Error, éxito y aviso tienen colores fijos para que se reconozcan siempre igual.
- **El color de algunos botones.** Cuando un color de marca no garantiza buena lectura, algunas pantallas usan un azul neutro para las acciones.
- **El diseño y los textos de los correos.** Personalizamos logo, imagen de cabecera, enlaces y nombre. El resto es común.
- **El remitente de los correos.** Se envían desde una dirección de la plataforma, no desde tu dominio.
- **Los textos de la app Wallet, la página de inicio de sesión y el portal de administración.** Solo el portal de emisión admite textos propios.
- **Idiomas distintos de los indicados.**

Si necesitas alguna de estas cosas, cuéntanoslo. Lo estudiamos como un proyecto aparte, con su propio plazo.

---

## Cómo lo ponemos en marcha

1. Nos envías el material de la lista de comprobación.
2. Lo revisamos: formatos, tamaños y contraste. Si algo no sirve, te lo devolvemos con lo que hay que cambiar.
3. Lo configuramos en el entorno de preproducción.
4. Lo revisas en preproducción y nos das tu visto bueno.
5. Lo pasamos a producción.

**Plazos**, contados desde que tenemos todo el material correcto. El plazo concreto te lo confirmamos al recibirlo:

- Marca de una organización nueva: forma parte de su alta en la plataforma y sigue el plan del alta.
- Una vez publicado, el cambio llega a tus usuarios en pocos minutos. La app Wallet lo muestra la siguiente vez que se abre con conexión.

---

## Lista de comprobación

Copia esta lista y envíanosla completa con los ficheros.

```text
Organización
[ ] Nombre comercial (20 caracteres o menos)
[ ] Persona de contacto para revisar la marca en preproducción

Logos e iconos
[ ] Logo para fondo de color (SVG o PNG transparente, 500 px de ancho o más)
[ ] Logo para fondo blanco (SVG o PNG transparente)
[ ] Favicon (SVG, o PNG de 32x32 y 180x180)
[ ] Icono de la app (PNG 512x512, motivo en el 80 % central)

Colores (hexadecimal #RRGGBB)
[ ] Principal y texto sobre el principal
[ ] Secundario y texto sobre el secundario
[ ] Tarjeta de atestación: inicial, final y texto (opcional)
[ ] Pantallas de acceso: inicial y final (opcional)
[ ] Contraste comprobado: 4,5:1 texto, 3:1 iconos y bordes

Idiomas
[ ] Idiomas de las interfaces e idioma por defecto
[ ] Idioma de los correos (español o inglés)

Soporte y legal
[ ] Página de soporte (https)
[ ] Correo de soporte
[ ] Teléfono, horario y plazo de respuesta
[ ] Centro de ayuda (https, opcional)
[ ] Aviso legal, privacidad, términos y accesibilidad (https)

Correos
[ ] Logo para correo (PNG transparente)
[ ] Imagen de cabecera 640x190 px (opcional)
[ ] Redes sociales (opcional)

Opcional
[ ] Textos propios del portal de emisión, por idioma
[ ] Aplicaciones que piden presentaciones: nombre, logo, web, privacidad y términos
[ ] Cabecera y pie HTML para la página de inicio de sesión
```

¿Dudas? [Contacta con soporte](../support.md).
