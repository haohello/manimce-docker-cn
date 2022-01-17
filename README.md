See the [main README](https://github.com/ManimCommunity/manim/blob/main/README.md) for some instructions on how to use this image.

# Building the image

The docker image corresponding to the checked out version of the git repository
can be built by running

```
docker build -t manimce-cn:latest -f docker/Dockerfile .

docker run -it --name manimcntr -v "$(pwd):/manim" manimce-cn /bin/bash
```

from the root directory of the repository.
