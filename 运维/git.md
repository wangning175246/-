查看远程地址

```shell
git remote -v
```

查看分支

```shell
git branch
```

设置远处的仓库的简单名字

```shell
git remote add origin https://gitlab.hhdc888.com/bob_front/agent_web.git
```

设置拉取的默认分支

```shell
 git branch --set-upstream-to=origin/dev dev
```

拉取指定的分支

```bash
git clone -b dev_jk http://10.1.1.11/service/tmall-service.git
```

查看提交记录

```bash
 git log
```

回滚到某个提交记录

```shell
git reset –hard 8ff24a6803173208f3e606e32dfcf82db9ac84d8
```

