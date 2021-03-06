﻿# Dependency Injector Benchmark Set

This project benchmark the following DI solutions:

* [Vanilla](https://github.com/greenlaw110/di-benchmark/blob/master/src/main/java/com/greenlaw110/di_benchmark/DIFactory.java#L24) 
* [Guice](https://github.com/google/guice) - 4.1.0
* [Genie](https://github.com/osglworks/java-di) - 0.2.0-SNAPSHOT
* [Feather](https://github.com/zsoltherpai/feather) - 1.0
* [Dagger](https://github.com/square/dagger) - 2.29.1
* [Pico](http://picocontainer.com/) - 2.15
* [jBeanBox](https://github.com/drinkjava2/jBeanBox) - 2.4.1
* [Spring](http://projects.spring.io/spring-framework/) - 4.3.2.RELEASE

## Object Graph

Below is the object graph of the class to be injected in the benchmark program

```
Object
└── A
    └── B
        └── C
            ├── D1
            └── D2
                └── E
```

All injection is done through Constructor injection

## Benchmarks

### Startup and first time fetch benchmark

```
Split Starting up DI containers & instantiating a dependency graph 4999 times:
-------------------------------------------------------------------------------
                     Vanilla| start:     2ms   fetch:     4ms
                       Guice| start:   425ms   fetch:   690ms
                     Feather| start:     7ms   fetch:    90ms
                      Dagger| start:     9ms   fetch:     3ms
                        Pico| start:   212ms   fetch:   263ms
                       Genie| start:   487ms   fetch:   152ms
              jBeanBoxNormal| start:     1ms   fetch:   264ms
            jBeanBoxTypeSafe| start:     2ms   fetch:   117ms
          jBeanBoxAnnotation| start:     0ms   fetch:   287ms
     SpringJavaConfiguration| start: 13641ms   fetch:   431ms
     SpringAnnotationScanned| start: 28109ms   fetch:   941ms
```

### Runtime bean injection benchmark

```
Runtime benchmark, fetch new bean for 50K times:
---------------------------------------------------------
                     Vanilla|     8ms
                       Guice|   117ms
                     Feather|    87ms
                      Dagger|     7ms
                       Genie|   101ms
                        Pico|   702ms
              jBeanBoxNormal|  1440ms
            jBeanBoxTypeSafe|   562ms
          jBeanBoxAnnotation|  1906ms
     SpringJavaConfiguration|  1235ms
     SpringAnnotationScanned|  1519ms
```

```
Runtime benchmark, fetch new bean for 5M times:
---------------------------------------------------------
                     Vanilla|   193ms
                       Guice|  2481ms
                     Feather|   915ms
                      Dagger|   106ms
                       Genie|   764ms
                        Pico| 12793ms
              jBeanBoxNormal| Timeout
            jBeanBoxTypeSafe| 53595ms
          jBeanBoxAnnotation| Timeout
     SpringJavaConfiguration| Timeout
     SpringAnnotationScanned| Timeout
```

```
Runtime benchmark, fetch singleton bean for 5M times:
---------------------------------------------------------
                     Vanilla|     4ms
                       Guice|   527ms
                     Feather|   147ms
                      Dagger|    10ms
                       Genie|   102ms
                        Pico|   325ms
              jBeanBoxNormal|    57ms
            jBeanBoxTypeSafe|    56ms
          jBeanBoxAnnotation|    73ms
     SpringJavaConfiguration|   226ms
     SpringAnnotationScanned|   185ms
```

## How to run the benchmark

You need JDK8 and [maven](http://maven.apache.org/) to run the benchmark program.

The project defined two profiles: `runtime` and `split_startup`.

```bash
#Run the `runtime` profile:
mvn clean compile exec:exec -Pruntime
```

This will run the Runtime benchmark, which fetch the bean of class `A` (as shown above) for `50K` times

```bash
#To run the `split_startup` profile:
mvn clean compile exec:exec -Psplit_startup
```

This will run the startup benchmark, which initialize the injector and load bean of class `A` for `5K` times.

To tune the warm up and iterations:

```
#For startup benchmark:
mvn clean compile exec:exec -Psplit_startup -Dstartup.iteration=100 -Dstartup.warmup=10
```

```
#For runtime benchmark:
mvn clean compile exec:exec -Pruntime -Druntime.iteration=1000 -Druntime.warmup=100
```

To benchmark singleton injection:

```
mvn clean compile exec:exec -Pruntime -Dsingleton=true
```

## Disclaim

The benchmark source code is originated from https://github.com/zsoltherpai/feather/tree/master/performance-test and adapted by [Gelin Luo](https://github.com/greenlaw110)
