# Personal webpage

You can test it on your local system by running:

```
git clone https://github.com/p-baldini/p-baldini.github.io.git
```

or:

```
git clone git@github.com:p-baldini/p-baldini.github.io.git
```

Then, pull the theme submodules:

```
git submodule update --init
```

If you have [Hugo](https://gohugo.io) installed on your system, simply run `hugo serve`, otherwise, this repo also contains a [pixi](https://pixi.prefix.dev) environment definition.
To use it, simply run:

```
pixi install
pixi shell
```

At this point, you should be able to use `hugo serve` also without a system wide installation.

## Maps

In order to update your maps, login with github to https://umap.openstreetmap.fr -- Not .org!
