# Fcch Blog - Hugo Framework

- [Fcch How To](https://blog.fcch.xyz/post/web/blog-hugo-framework-github-pages/)

## Requisitos

```bash
$ hugo version
```

Usar una version reciente de Hugo Extended (actualmente el proyecto compila con `v0.163.x`).

## Iniciando Proyecto 

```bash
# Iniciar Proyecto Hugo
$ hugo new site fcchn4.github.io

# Dentro del Proyecto Hugo
$ cd fcchn4.github.io
$ echo public > .gitignore
$ git init
$ git remote add origin git@github.com:fcchn4/fcchn4.github.io.git
```

## Agregando un Theme

```bash
# Agregando Theme como un submodulo
$ git submodule add git@github.com:fcchn4/hugo-clarity.git themes/hugo-clarity
$ echo 'theme = "hugo-clarity"' >> config.toml

# Para Probar el nuevo proyecto Hugo
$ hugo server -D --logLevel info

# Build de validacion (local)
$ hugo --gc --minify --logLevel warn

# Subir cuerpo completo del proyecto
$ git add .
$ git commit -m 'first hugo'
$ git push origin main
```

## Creando rama para despliegue

```bash
# Creamos una rama "deploy" para desplegar el sitio
$ git checkout --orphan deploy
$ git add .
$ git commit -m 'deploy branch'
$ git push origin deploy 
```

## Estableciendo rama deploy como contenido web estatico

```bash
# Cambio de rama deploy
$ git checkout main
$ mkdir public
$ git worktree add -B deploy public origin/deploy

# Borrando contenido de rama deploy
$ cd public
$ rm -r * && rm -r .gitignore .gitmodules
```

## Clonar el repositorio

```bash
$ git clone --recurse-submodules git@github.com:fcchn4/fcchn4.github.io.git
$ cd fcchn4.github.io
$ mkdir public
$ git worktree add -B deploy public origin/deploy
```

## Despliegue de un nuevo artículo

```bash
# 1. Asegúrate de tener el worktree configurado
#    (solo la primera vez o después de clonar)
$ mkdir -p public
$ git worktree add -B deploy public origin/deploy

# 2. Genera el sitio (desde la raíz del proyecto en main)
$ hugo --gc --minify --logLevel warn

# 3. Entra al directorio public (que es la rama deploy)
$ cd public

# 4. Commit y push (el CNAME ya existe ahí y no se toca)
$ git add .
$ git commit -m "Nuevo artículo: título del artículo"
$ git push origin deploy

# 5. Vuelve a la raíz
$ cd ..
```

## Comandos utiles

```bash
# Servidor local
$ hugo server -D --disableFastRender --logLevel info

# Build produccion
$ hugo --gc --minify --logLevel warn

# Config efectiva que Hugo esta aplicando
$ hugo config
```

## Nota de compatibilidad

- Evitar `--verbose` (flag removida en Hugo moderno).
- Usar `--logLevel` con valores: `debug`, `info`, `warn`, `error`.
