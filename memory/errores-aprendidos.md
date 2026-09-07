# Errores aprendidos — remate-web

> Lo mas reciente arriba. Que fallo · causa raiz · como se resolvio · regla para la proxima.

## 2026-09-07 — Bajar el precio dejo texto derivado que ya no era cierto

**Que fallo:** al bajar el rack de crossfit a Gs. 8.500.000, cambiar el monto no alcanzaba:
la pagina seguia diciendo "te ahorras Gs. 22.500.000", "64% menos" y el titular "Menos de la
mitad de su precio" — los tres calculados sobre el precio viejo. Se detecto y corrigio antes
de pushear, pero el 28/08 (sofas) ya habia pasado lo mismo.

**Causa raiz:** el precio no vive en un solo lugar ni en una variable. Esta escrito a mano en
5 puntos (meta description, og:description, tarjeta de oferta, h2 del CTA final, card del
indice raiz) y ademas hay **texto derivado** — el monto ahorrado, el porcentaje y frases como
"menos de la mitad" — que un buscar-y-reemplazar del monto no toca.

**Como se resolvio:** se recalculo a mano (35.000.000 - 8.500.000 = 26.500.000, o sea 76%) y
el titular paso a "Menos de la cuarta parte de su precio", tambien en el og:title.

**Regla:** despues de cambiar un precio, greppear el monto viejo Y el ahorro, el `%` y las
frases del tipo "mitad"/"menos de" en todo el repo. El ahorro se calcula, no se copia.

## 2026-08-28 — La web seguia con los precios viejos despues del push

**Que fallo:** se actualizaron los precios (sofas a Gs. 3.900.000, rack a Gs. 12.500.000),
se commiteo y pusheo a master, y se reporto "ya esta arriba". Ivan aviso que la web seguia
mostrando 6.500.000 y 15.000.000.

**Causa raiz:** el proyecto de Cloudflare Pages `remate-ivanlafuente` estaba creado como
**Direct Upload**, sin Git conectado — a diferencia de los otros 5 sitios. Un push no
dispara ningun build. Segundo tropiezo encadenado: el primer `wrangler pages deploy` se hizo
con `--branch=main` y quedo como *Preview*, porque la branch de produccion del proyecto era
`master`; wrangler dijo "Deployment complete" y el dominio seguia sirviendo lo viejo.

**Como se resolvio:** Cloudflare no deja convertir un Direct Upload a Git
("You cannot update the `source` object in a Direct Uploads project"), asi que se creo el
proyecto **`remate-web`** conectado al repo por API (production_branch `master`), se movio
el dominio `remate.lacasonalafuente.com`, se apunto el CNAME a `remate-web-bv2.pages.dev` y
Ivan agrego el repo a la GitHub App *Cloudflare Pages* en github.com/settings/installations
(conectar el repo por API NO configura eso solo). Auto-deploy verificado: trigger
`github:push` -> deploy success. El proyecto viejo se borro.

**Regla:** un `git push` no es prueba de nada. Antes de decir "esta arriba":
`curl -s https://remate.lacasonalafuente.com/<ruta>?v=$RANDOM | grep <dato nuevo>`.
Y ante "sigue apareciendo lo viejo" no culpar al cache: mirar primero el deploy
(`wrangler pages deployment list` -> branch y estado).
