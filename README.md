# Fcch Blog - Hugo Framework

- [Fcch How To](https://blog.fcch.xyz/posts/2020/01/blog-con-hugo-framework-y-github-pages/)

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
$ git submodule add git@github.com:fcchn4/hugo-theme-hello-friend-ng.git themes/hello-friend-ng
$ echo 'theme = "hello-friend-ng"' >> config.toml

# Para Probar el nuevo proyecto Hugo
$ hugo server --watch -D

# Subir cuerpo completo del proyecto
$ git add .
$ git commit -m 'first hugo'
$ git push origin master
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
$ git checkout master
$ mkdir public
$ git worktree add -B deploy public origin/deploy

# Borrando contenido de rama deploy
$ cd public
$ rm -r * && rm -r .gitignore .gitmodules
```

## Clonar el repositorio

```bash
$ git clone --recurse-submodules git@github.com:fcchn4/fcchn4.github.io.git
$ mkdir public
$ git worktree add -B deploy public origin/deploy
```
