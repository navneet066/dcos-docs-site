---
layout: layout.pug
navigationTitle:  dcos package repo add
title: dcos package repo add
menuWeight: 3
excerpt: 将软件包存储库添加到 DC/OS
enterprise: false
---

# 说明
`dcos package repo add` 命令让您可以将软件包存储库添加到 DC/OS。

# 使用

```bash
dcos package repo add <repo-name> <repo-url> [--index=<index>]
```

# 选项

| 名称 | 说明 |
|---------|-------------|
| `-h`, `--help` | 显示用法。|
| `--index=<index>` | 软件包存储库列表中的数字位置。按降序搜索软件包存储库。默认情况下，Universe 存储库首先在列表中。|

## 位置自变量

| 名称 | 说明 |
|---------|-------------|
| `<repo-name>` | 软件包存储库的名称。例如， `Universe`。|
| `<repo-url>` | 软件包存储库的 URL。例如，https://universe.mesosphere.com/repo。|


# 示例

有关示例，请参阅[文档](/cn/1.12/administering-clusters/repo/)。

# 父命令

| 命令 | 说明 |
|---------|-------------|
| [dcos package](/cn/1.12/cli/command-reference/dcos-package/) | 安装和管理 DC/OS 软件包。|