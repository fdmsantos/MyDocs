# Start Server

```bash
py -m mkdocs serve
```

# Build

```sh
mkdocs build -d docs
```

# Deploy

```sh
docker run -d -it -p 8000:8000 -v ${PWD}:/docs --name MyDocs squidfunk/mkdocs-material
```

# Link
[Link](http://127.0.0.1:8000)

[GithubPages](https://fdmsantos.github.io/MyDocs/docs/)