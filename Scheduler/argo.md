CRD：Custom Resource Definition

- Define workflows where each step in the workflow is a container
- DAG

Cloud Native Compution Foundation.



## Workflow

- 定义工作流如何被执行
- 保存工作流的状态？？？



## Workflow Spec

### templates

可以认为是一组函数，定义执行指令

#### Template Definitions

定义将被执行的任务，通常是一个容器(Container)

**C**

最常见的模板类型，该类型的模板会调度一个容器

例子：

```yaml
  - name: whalesay
    container:
      image: docker/whalesay
      command: [cowsay]
      args: ["hello world"]
```

**SCRIPT**

相比于容器模板，多了一个source，可以用来定义一个内联的脚本，脚本会被保存到一个文件中被执行，脚本执行结果会保存到Argo参数(Argo Variable)列表中，根据脚本如何被执行保存到{{tasks.\<name\>.outputs.result}或者{{steps.\<NAME\>.outputs.result}}中

```yaml
  - name: gen-random-int
    script:
      image: python:alpine3.6
      command: [python]
      source: |
        import random
        i = random.randint(1, 100)
        print(i)
```

**RESOURCE**

直接操作集群资源

如：get/create/apply/delete/replace/patch

```yaml
  - name: k8s-owner-reference
    resource:
      action: create
      manifest: |
        apiVersion: v1
        kind: ConfigMap
        metadata:
          generateName: owned-eg-
        data:
          some: value
```

**SUSPEND**

挂起执行

```yaml
  - name: delay
    suspend:
      duration: "20s"
```

#### Template Invocators

用来调取其它模板(invoke/call other templates and provide execution)

**STEPS**

定义执行步骤

Outer lists will run sequentially and inner lists will run in parallel.

```yaml
  - name: hello-hello-hello
    steps:
    - - name: step1
        template: prepare-data
    - - name: step2a
        template: run-data-first-half
      - name: step2b
        template: run-data-second-half
```

**DAG**

可以通过DAG低于类似于图的依赖关系

```yaml
  - name: diamond
    dag:
      tasks:
      - name: A
        template: echo
      - name: B
        dependencies: [A]
        template: echo
      - name: C
        dependencies: [A]
        template: echo
      - name: D
        dependencies: [B, C]
        template: echo
```



### entrypoint

定义main函数