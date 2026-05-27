# **Auditoría ASG y Refactorización Sostenible**

**Empresa auditada:** Auténtico Döner Kebap **URL:** https://www.autenticodonerkebap.com 

**Módulo:** Sostenibilidad Aplicada al Sistema Productivo 

**Autor:** *(Pepe Gil Cué)* **Fecha:** 6 Mayo de 2026

## **Fase 1 \ Dimensión Ambiental (A)**

### **Medición inicial**

Para esta parte usé Website Carbon Calculator y también Lighthouse desde Chrome. Los resultados podrían ser mejorables para una web de un restaurante local.

| Métrica | Resultado |
| ----- | ----- |
| Peso total de la página | 4,8 MB |
| CO₂ estimado por visita | 0,23 g CO2 |
| Puntuación Lighthouse Performance | 78 / 100 |
| Indice de similitud | 53% |

Según Website Carbon, lo recomendable es no superar los 0,5 g de CO₂ por visita, por lo tanto se situa dentro de lo recomendado.

<img width="1248" height="739" alt="Captura de pantalla 2026-05-13 124756" src="https://github.com/user-attachments/assets/ea8776c5-0258-48ca-a897-c06660399f46" />

<img width="1028" height="733" alt="Captura de pantalla 2026-05-13 131103" src="https://github.com/user-attachments/assets/8348e9c9-3abc-492c-87db-4915e90d96b5" />

### **Identificación de Bloatware**

Miré la pestaña Network de las DevTools de Chrome con la caché activada para ver realmente el peso de la web. La web está hecha con Wix, lo cual ya de por sí añade mucho peso que no se puede controlar.

| Recurso | Tipo | Tamaño aprox. | Problema |
| ----- | ----- | ----- | ----- |
| Imagen de portada principal (Wixstatic) | Imagen | \~1,5 MB | La imagen de cabecera se descarga a resolución completa aunque en móvil se vea mucho más pequeña |
| Scripts de Wix  | JavaScript | \~1,8 MB | Wix carga un montón de JavaScript de su plataforma que el usuario no necesita para ver una web de kebab |
| Imagen del banner de especias  | Imagen | \~900 KB | La foto del banner inferior tiene resolución de 1679x534 y se descarga entera aunque en muchas pantallas se muestre recortada |

Estos tres tipos de recursos concentran la mayor parte del peso de la página.

### **¿Sufre de "inflación de software"?**

Sí, bastante. El principal problema de esta web es que está hecha con Wix, que es un constructor de páginas que añade automáticamente muchísimo JavaScript aunque la web sea muy sencilla. Para una web que básicamente solo muestra texto, fotos y la lista de locales, cargar casi 2 MB solo de scripts de la plataforma lo cual es excesivo.

Además las imágenes, aunque Wix las sirve con algo de compresión automática, siguen siendo bastante pesadas y en algunos casos se descargan a resoluciones mayores de las necesarias. Todo esto hace que la web tarde bastante en cargar y tenga un impacto de carbono mucho más alto del que debería.

## **Fase 2 \ Dimensión Social y Equidad** 

### **Test de accesibilidad**

Usé WAVE y la pestaña Accessibility de Lighthouse.

| Herramienta | Resultado |
|---|---|
| WAVE | Varios errores de accesibilidad en imágenes y estructura  |
| Lighthouse Accessibility | Nivel de accesibilidad mejorable según la web |
| Etiquetas ALT | Algunas imágenes pueden carecer de descripciones adecuadas |

<img width="1904" height="839" alt="Captura de pantalla 2026-05-13 132723" src="https://github.com/user-attachments/assets/de0ad427-54a8-4558-8909-da53c15e940b" />

### **Barreras encontradas**

**De todos los encontrados, a destacar y menos habituales son:**

**Problema 1 — Imágenes sin atributo alt descriptivo (WCAG 2.2, criterio 1.1.1)**

Varias imágenes de la web tienen como texto alternativo el nombre del archivo original, simplemente están vacías. Esto es un problema grave porque un usuario con lector de pantalla escucha ese nombre de archivo en lugar de una descripción útil asociada a la imagen. El logo que aparece en la cabecera y el pie de página usa "Avatar \#02 sin fondo.png" como alt, lo cual no le dice nada a nadie.
```
\<\!-- como está ahora \--\>  
\<img src="https://static.wixstatic.com/media/f30d39\_c577b028365e41ac913badfd327ca48d\~mv2.png"  
     alt="Avatar \#02 sin fondo.png"\>

\<\!-- como debería estar \--\>  
\<img src="https://static.wixstatic.com/media/f30d39\_c577b028365e41ac913badfd327ca48d\~mv2.png"  
     alt="Logo de Auténtico Döner Kebap"\>
```
Y para las imágenes decorativas del carrusel que no aportan información:
```
\<\!-- como está ahora \--\>  
\<img src="f30d39\_2c44a80bd0a34782bb4aec8472ec6200\~mv2.jpg" alt="Vista General"\>

\<\!-- como debería estar (descripción más útil) \--\>  
\<img src="f30d39\_2c44a80bd0a34782bb4aec8472ec6200\~mv2.jpg"  
     alt="Vista general del interior de uno de los locales de Auténtico Döner Kebap en Sevilla"\>
```
**Problema 2 — Formulario de contacto sin etiquetas label**

El formulario de la página de contacto está generado por Wix y los campos no tienen etiquetas \<label\> visibles asociadas correctamente. Solo hay un texto encima del formulario y un checkbox de "Acepto los términos y condiciones" sin que quede claro qué campos hay que rellenar. Para alguien que use lector de pantalla el formulario es prácticamente imposible de usar.
```
\<\!-- como está generado por Wix (sin labels asociados) \--\>  
\<input type="text" placeholder="Nombre"\>  
\<input type="email" placeholder="Email"\>  
\<textarea placeholder="Mensaje"\>\</textarea\>  
\<input type="checkbox"\> Acepto los términos y condiciones

\<\!-- como debería estar \--\>  
\<label for="nombre"\>Nombre\</label\>  
\<input type="text" id="nombre" name="nombre" placeholder="Tu nombre"\>

\<label for="email"\>Email\</label\>  
\<input type="email" id="email" name="email" placeholder="tu@email.com"\>

\<label for="mensaje"\>Mensaje\</label\>  
\<textarea id="mensaje" name="mensaje" placeholder="Escribe tu mensaje aquí"\>\</textarea\>

\<label\>  
  \<input type="checkbox" name="terminos" required\>  
  Acepto los \<a href="/aviso-legal"\>términos y condiciones\</a\>  
\</label\>
```
## **Fase 3 \ Dimensión de Gobernanza y Ética** 

### **Transparencia con las cookies**

<img width="1902" height="578" alt="Captura de pantalla 2026-05-13 133622" src="https://github.com/user-attachments/assets/d99c8369-cf51-4f55-aa38-9116463e4e31" />

La web tiene aviso legal, política de privacidad y política de cookies enlazados en el pie de página, pero no aparece ningún banner de consentimiento de cookies al entrar. Esto es problemático porque la web usa scripts de terceros que colocan cookies antes de que el usuario haya dado su consentimiento. Según el RGPD, las cookies no esenciales no se pueden activar antes de que el usuario las acepte.

El hecho de que no haya banner visible no significa que respeten la normativa, según la información es al contrario, significa que las cookies se están cargando sin consentimiento previo, lo cual es peor.

### **Datos personales en el formulario**

El formulario de contacto de la página /contact pide los siguientes datos:

| Campo visible | ¿Necesario? |
| ----- | ----- |
| Nombre | Sí |
| Email | Sí |
| Mensaje | Sí |
| Checkbox de términos | Correcto |

En este caso el formulario es bastante sencillo y no pide datos innecesarios, lo cual está bien. El problema es que al aceptar los términos el usuario no sabe exactamente a qué está consintiendo porque el enlace a los términos lleva a una página de aviso legal genérico que no especifica cómo se van a usar los datos del formulario.

# Fase 4 — Propuesta de Refactorización (unicamente lo destacado)

## Refactorización con HTML Semántico

Uno de los principales problemas detectados en la web actual es la gran cantidad de contenedores genéricos (`div`) generados automáticamente por Wix. Esto provoca un HTML poco semántico, más difícil de interpretar por navegadores y lectores de pantalla.

La propuesta sería migrar a una estructura basada en HTML semántico, utilizando etiquetas específicas según el contenido de cada sección. Esto mejoraria  la mantenibilidad del código, la eficiencia del navegador al interpretar la página.

Ejemplo de la estructura semántica propuesta:

```html
<body>

  <header>
    <nav>
      <img src="logo.png"
           alt="Logo de Auténtico Döner Kebap"
           loading="eager">
           
      <ul>
        <li><a href="#inicio">Inicio</a></li>
        <li><a href="#menu">Menú</a></li>
        <li><a href="#locales">Locales</a></li>
        <li><a href="#contacto">Contacto</a></li>
      </ul>
    </nav>
  </header>

  <main>

    <section id="inicio">
      <h1>Auténtico Döner Kebap</h1>
      <p>Comida inspirada en la tradición turca.</p>
    </section>

    <section id="menu">
      <article>
        <h2>Döner Especial</h2>
        <p>Carne marinada con especias tradicionales.</p>
      </article>

      <article>
        <h2>Durum Mixto</h2>
        <p>Ternera y pollo con verduras frescas.</p>
      </article>
    </section>

    <section id="galeria">
      <picture>
        <source srcset="local.avif" type="image/avif">
        <source srcset="local.webp" type="image/webp">

        <img src="local.jpg"
             alt="Interior del restaurante"
             loading="lazy"
             width="800"
             height="500">
      </picture>
    </section>

  </main>

  <footer>
    <p>© 2026 Auténtico Döner Kebap</p>
  </footer>

</body>
```
Con esta estructura:
- `<header>` agrupa la cabecera y navegación.
- `<nav>` identifica el menú principal.
- `<main>` contiene el contenido principal.
- `<section>` divide las distintas áreas de la página.
- `<article>` encapsula contenidos independientes como productos o platos.
- `<footer>` contiene información final y de contacto.

---

## Optimización de imágenes

Wix ya sirve algunas imágenes en formato AVIF automáticamente (visible en las URLs mediante `enc_avif`), lo cual es positivo porque AVIF es un formato moderno mucho más eficiente. Sin embargo, no todas las imágenes están optimizadas ni adaptadas al tamaño real del dispositivo.

La propuesta sería utilizar siempre `<picture>` con imágenes responsivas en AVIF y WebP:

```
<!-- como lo sirve Wix actualmente -->
<img src="imagen.jpg" alt="">

<!-- propuesta optimizada -->
<picture>

  <source
    srcset="
      especias-banner-480.avif 480w,
      especias-banner-900.avif 900w,
      especias-banner-1679.avif 1679w"
    type="image/avif">

  <source
    srcset="
      especias-banner-480.webp 480w,
      especias-banner-900.webp 900w,
      especias-banner-1679.webp 1679w"
    type="image/webp">

  <img
    src="especias-banner-1679.jpg"
    alt="Especias utilizadas en la preparación del auténtico Döner Kebap"
    width="1679"
    height="534"
    loading="lazy">

</picture>
```

También se implementaría Lazy Loading para las imágenes situadas fuera del primer pantallazo:

```html
<!-- visible al cargar -->
<img src="logo.png"
     alt="Logo de Auténtico Döner Kebap"
     loading="eager">

<!-- imágenes secundarias -->
<img src="vista-general.webp"
     alt="Vista general del local"
     loading="lazy"
     width="97"
     height="97">
```

Esto reduce el consumo de datos y mejora el tiempo de carga inicial.

---

## Reducción de peticiones y scripts innecesarios

El principal problema detectado es la propia arquitectura de Wix. La plataforma carga automáticamente numerosos scripts y recursos que no pueden eliminarse manualmente.

Para conseguir una mejora real en sostenibilidad y rendimiento, la propuesta seria migrar a un HTML semántico 

Mientras se mantenga Wix, únicamente podrían eliminarse widgets o aplicaciones instaladas que no se utilicen, que no disminuira sus consumo en gran cantidad.

Además, algunos efectos implementados mediante JavaScript podrían sustituirse por CSS nativo. Por ejemplo, el scroll suave:

```css
html {
  scroll-behavior: smooth;
}
```
Esto evita cargar librerías adicionales como jQuery para funcionalidades simples. 
**Comparativa estimada antes / después**

| Métrica | Web creada con constructor visual (Wix) | HTML semántico |
|---|---|---|
| Peso total aproximado | Alto (muchos scripts y recursos cargados automáticamente) | Bajo (solo archivos necesarios) |
| Velocidad de carga | Media | Alta |
| Consumo energético estimado | Mayor | Menor |
| Lighthouse Performance | Variable / normalmente media | Alta si está bien optimizada |
| Dependencia de terceros | Elevada | Reducida |
| Control sobre optimización | Limitado | Completo |

### **Reflexión sobre la Paradoja de Jevons**

La Paradoja de Jevons dice que mejorar la eficiencia de algo que puede acabar aumentando el consumo total porque al ser más rápido y útil lo usa más gente. Aplicado aquí: si la web de Auténtico Döner Kebap cargará mucho más rápido podría aparecer mejor posicionada en Google.

Para evitar eso haría tres cosas. Primero, contratar el hosting en un proveedor con energía 100% renovable. Segundo, no añadir funcionalidades nuevas a la web si no son necesarias. Tercero, medir el éxito no solo por visitas sino por usuarios que completan su objetivo (encontrar el local más cercano, consultar los alérgenos, y sobre todo de manera rápida y eficaz).

## **Herramientas utilizadas**

* Website Carbon Calculator (websitecarbon.com)  
* Lighthouse — Chrome DevTools  
* WAVE Web Accessibility Evaluation Tool (wave.webaim.org)  
* Chrome DevTools pestaña Network  
* PageSpeed Insights (pagespeed.web.dev)  
* Documentos Google (realización la documentación)  
* GitHub (almacenamiento de documentos en un repositorio)

## **Referencias**

* WCAG 2.2: https://www.w3.org/TR/WCAG22/  
* RGPD — Reglamento UE 2016/679  
* Sustainable Web Design: https://sustainablewebdesign.org  
* Web auditada: https://www.autenticodonerkebap.com
