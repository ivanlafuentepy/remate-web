# Errores aprendidos — remate-web

> Lo mas reciente arriba. Que fallo · causa raiz · como se resolvio · regla para la proxima.

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
