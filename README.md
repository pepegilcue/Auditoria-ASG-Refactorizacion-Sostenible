# **Auditoría ASG y Refactorización Sostenible**

**Empresa auditada:** Auténtico Döner Kebap **URL:** https://www.autenticodonerkebap.com **Módulo:** Sostenibilidad Aplicada al Sistema Productivo — Unidad 6 **Autor:** *(Pepe Gil Cué)* **Fecha:** 6 Mayo de 2026

## **Fase 1 \- Dimensión Ambiental (A)**

### **Medición inicial**

Para esta parte usé Website Carbon Calculator y también Lighthouse desde Chrome. Los resultados la verdad que son bastante malos para lo que es una web de un restaurante local.

| Métrica | Resultado |
| ----- | ----- |
| Peso total de la página | 4,8 MB |
| CO₂ estimado por visita | 0,23 g |
| Puntuación Lighthouse Performance | 78 / 100 |

Según Website Carbon, lo recomendable es no superar los 0,5 g de CO₂ por visita, así que esta web lo supera por mucho.

*![][image1]*

*![][image2]*

### **Identificación de Bloatware**

Miré la pestaña Network de las DevTools de Chrome con la caché activada para ver realmente el peso de la web. La web está hecha con Wix, lo cual ya de por sí añade mucho peso que no se puede controlar.

| Recurso | Tipo | Tamaño aprox. | Problema |
| ----- | ----- | ----- | ----- |
| Imagen de portada principal (Wixstatic) | Imagen | \~1,5 MB | La imagen de cabecera se descarga a resolución completa aunque en móvil se vea mucho más pequeña |
| Scripts de Wix  | JavaScript | \~1,8 MB | Wix carga un montón de JavaScript de su plataforma que el usuario no necesita para ver una web de kebab |
| Imagen del banner de especias  | Imagen | \~900 KB | La foto del banner inferior tiene resolución 1679x534 y se descarga entera aunque en muchas pantallas se muestre recortada |

Estos tres tipos de recursos concentran la mayor parte del peso de la página.

### **¿Sufre de "inflación de software"?**

Sí, y bastante. El principal problema de esta web es que está hecha con Wix, que es un constructor de páginas que añade automáticamente muchísimo JavaScript y CSS propio aunque la web sea muy sencilla. Para una web que básicamente solo muestra texto, fotos y la lista de locales, cargar casi 2 MB solo de scripts de la plataforma lo cual  es excesivo.

Además las imágenes, aunque Wix las sirve con algo de compresión automática, siguen siendo bastante pesadas y en algunos casos se descargan a resoluciones mayores de las necesarias. Todo esto hace que la web tarde bastante en cargar y tenga un impacto de carbono mucho más alto del que debería.

## **Fase 2 — Dimensión Social y Equidad** 

### **Test de accesibilidad**

Usé WAVE y la pestaña Accessibility de Lighthouse.

| Herramienta | Resultado |
| ----- | ----- |
| WAVE | Varios errores en imágenes y estructura |
| Lighthouse Accessibility | Puntuación por debajo de 80 |

*![][image3]*

### **Barreras encontradas**

**De todos los encontrados, a destacar y menos habituales son:**

**Problema 1 — Imágenes sin atributo alt descriptivo (WCAG 2.2, criterio 1.1.1)**

Varias imágenes de la web tienen como texto alternativo el nombre del archivo original, simplemente están vacías. Esto es un problema grave porque un usuario con lector de pantalla escucha ese nombre de archivo en lugar de una descripción útil asociada a la imagen. El logo que aparece en la cabecera y el pie de página usa "Avatar \#02 sin fondo.png" como alt, lo cual no le dice nada a nadie.

\<\!-- como está ahora \--\>  
\<img src="https://static.wixstatic.com/media/f30d39\_c577b028365e41ac913badfd327ca48d\~mv2.png"  
     alt="Avatar \#02 sin fondo.png"\>

\<\!-- como debería estar \--\>  
\<img src="https://static.wixstatic.com/media/f30d39\_c577b028365e41ac913badfd327ca48d\~mv2.png"  
     alt="Logo de Auténtico Döner Kebap"\>

Y para las imágenes decorativas del carrusel que no aportan información:

\<\!-- como está ahora \--\>  
\<img src="f30d39\_2c44a80bd0a34782bb4aec8472ec6200\~mv2.jpg" alt="Vista General"\>

\<\!-- como debería estar (descripción más útil) \--\>  
\<img src="f30d39\_2c44a80bd0a34782bb4aec8472ec6200\~mv2.jpg"  
     alt="Vista general del interior de uno de los locales de Auténtico Döner Kebap en Sevilla"\>  
**Problema 2 — Formulario de contacto sin etiquetas label (WCAG 2.2, criterio 1.3.1)**

El formulario de la página de contacto está generado por Wix y los campos no tienen etiquetas \<label\> visibles asociadas correctamente. Solo hay un texto encima del formulario y un checkbox de "Acepto los términos y condiciones" sin que quede claro qué campos hay que rellenar. Para alguien que use lector de pantalla el formulario es prácticamente imposible de usar.

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

## **Fase 3 \- Dimensión de Gobernanza y Ética** 

### **Transparencia con las cookies**

![][image4]

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

## **Fase 4 \- Propuesta de Refactorización (Green Coding)**

### **Optimización de imágenes**

Wix ya sirve algunas imágenes en AVIF automáticamente (se puede ver en las URLs con avif), lo cual es positivo. Ya que es un formato de imagen moderno y de próxima generación diseñado para ser mucho más eficiente.  El problema es que no lo hace con todas y las dimensiones no siempre están optimizadas para el dispositivo que las muestra.

La propuesta sería, si se migrará a una plataforma propia, usar siempre el elemento \<picture\>con AVIF y WebP:

\<\!-- como lo sirve Wix actualmente (solo una versión de la imagen) \--\>  
\<img src="https://static.wixstatic.com/media/f30d39\_a90c5e2cd4b44229875cfe3ae7834f67\~mv2.jpg/v1/fill/w\_1679,h\_534,al\_c,q\_85,usm\_0.66\_1.00\_0.01,enc\_avif,quality\_auto/Especias%20mini.jpg"  
     alt=""\>  
\<\!-- como debería estar con control total \--\>  
\<picture\>  
  \<source  
    srcset="especias-banner-480.avif 480w, especias-banner-900.avif 900w, especias-banner-1679.avif 1679w"  
    type="image/avif"\>  
  \<source  
    srcset="especias-banner-480.webp 480w, especias-banner-900.webp 900w, especias-banner-1679.webp 1679w"  
    type="image/webp"\>  
  \<img src="especias-banner-1679.jpg"  
       alt="Especias utilizadas en la preparación del auténtico Döner Kebap"  
       width="1679" height="534"  
       loading="lazy"\>  
\</picture\>

Sí implementaría Lazy Loading para todas las imágenes que están por debajo del primer pantallazo, ya que la web tiene bastante scroll y muchas imágenes que no hace falta cargar de entrada:

\<\!-- logo del header: carga inmediata porque está visible desde el principio \--\>  
\<img src="logo.png" alt="Logo de Auténtico Döner Kebap" loading="eager"\>

\<\!-- imágenes del carrusel y el resto de la página: carga diferida \--\>  
\<img src="vista-general.webp"  
     alt="Vista general del local"  
     loading="lazy"  
     width="97" height="97"\>

### **Reducción de peticiones**

El problema principal aquí es Wix en sí. Al estar la web construida sobre esa plataforma, no hay manera de eliminar los scripts que Wix carga automáticamente. Si el negocio quisiera una web más eficiente tendría que migrar a una solución propia o a un CMS más ligero como WordPress con un tema minimalista, o directamente a HTML/CSS estático.

Dentro de lo que se puede mejorar sin cambiar de plataforma: en Wix se pueden desactivar algunas aplicaciones y widgets instalados que no se usan. Si hubiera alguna app de Wix Market instalada y sin usar, eliminarla reduciría las peticiones.

Si se migrara a web propia, el script de scroll suave de jQuery (que Wix incluye en muchas webs) se reemplazaría con CSS:

html {  
  scroll-behavior: smooth;  
}  
**Comparativa estimada antes / después (si se migrara a web estática propia):**

| Métrica | Situación actual (Wix) | Web estática optimizada |
| ----- | ----- | ----- |
| Peso total |  |  |
| CO₂ por visita |  |  |
| estimado |  |  |
| Lighthouse Performance |  |  |

### **Reflexión sobre la Paradoja de Jevons**

La Paradoja de Jevons dice que mejorar la eficiencia de algo puede acabar aumentando el consumo total porque al ser más rápido y útil lo usa más gente. Aplicado aquí: si la web de Auténtico Döner Kebap cargará mucho más rápido podría aparecer mejor posicionada en Google.

Para evitar eso haría tres cosas. Primero, contratar el hosting en un proveedor con energía 100% renovable, así el aumento de tráfico no se traduce en más emisiones. Segundo, no añadir funcionalidades nuevas a la web si no son necesarias. Tercero, medir el éxito no solo por visitas sino por usuarios que completan su objetivo (encontrar el local más cercano, consultar los alérgenos).

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


