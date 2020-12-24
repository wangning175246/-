pod中启动的第一个容器Infra容器，他占用极少的资源，他主要提供使pod内的容器可以使用相同的网络名称空间。

容器设计模式

* 基础环境镜像和jar，war包分离，

  ```yaml
  apiVersion: v1
  kind: Pod
  metadata:
    name: two-containers
  spec:
    restartPolicy: Never
    volumes:
      - name: shared-data
    hostPath:
      path: /data
    containers:
    - name: nginx-container
      image: nginx
      volumeMounts:
      - name: shared-data
      mountPath: /usr/share/nginx/html
    - name: debian-container
      image: debian
      volumeMounts:
      - name: shared-data
        mountPath: /pod-data
      command: ["/bin/sh"]
      args: ["-c", "echo Hello from the debian container > /pod-data/index.html"]
  ```

* 日志收集