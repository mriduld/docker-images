## Run command


```
docker run --rm -it --name spark \
  -p 4040:4040 \
  -v ~/workspace:/app \
  -v ~/.sbt:/root/.sbt \
  -v ~/.ivy2:/root/.ivy2 \
  -v ~/.coursier:/root/.coursier \
  -v ~/.m2/repository:/root/.m2/repository \
   de-spark-dataproc-1.4.14-debian9:latest bash
```
