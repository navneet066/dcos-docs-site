---
layout: layout.pug
navigationTitle: 群集链接
title: 群集链接
menuWeight: 0
excerpt: 管理群集链路
enterprise: true
---

群集链接是群集和另一群集之间的 **单向** 关系。

您可以使用 DC/OS CLI [dcos cluster link](/cn/1.12/cli/command-reference/dcos-cluster/dcos-cluster-link/) 和 [dcos cluster unlink](/cn/1.12/cli/command-reference/dcos-cluster/dcos-cluster-unlink/) 命令和 [cluster link API](/cn/1.12/administering-clusters/multiple-clusters/cluster-link-api/)添加和删除一个群集到另一个群集的链接。设置链接后，您可以使用 CLI 或 UI 轻松在群集之间切换。如果已经使用 SSO 提供程序设置链接，您不需要提供凭证即可切换群集。

您必须是超级用户或具有相应的群集链路 [权限](/cn/1.12/security/ent/perms-reference/#cluster-linker)才能查看、添加和删除链路并授予查看已连接群集的权限。


# 启用使用 SSO 访问群集链接

作为超级用户：

1. 配置 [OpenID IDP](/cn/1.12/security/ent/sso/setup-openid/)。
    1. 确保在 Google Dev 控制台中的**授权 Javascript 源** 和 **授权重定向 URL** 字段中均提供两个群集 URL。
    1. 提供 OIDC 名称，如“google-idp”。
    1. 配置 OIDC 时，确保两个群集使用相同的 `Client-Id` 和 `Client-Secret` 。
1. 为每个用户提供查看服务和链接群集的权限：

    1. 选择 **组织 -> 用户**。
    1. 选择用户。
    1. 单击 **添加权限**。
    1. 在右上方，单击 **插入权限字符串**。
    1. 粘贴权限：

        ```
        dcos:adminrouter:ops:mesos full
        dcos:adminrouter:ops:slave full
        dcos:adminrouter:service:marathon full
        dcos:service:marathon:marathon:services:/ full
        dcos:cluster:linker:* read
        ```

 1. 单击 **添加权限**。

# 添加群集链接

要添加指向另一个群集的链接，运行 `dcos cluster link` 命令，提供群集的 URL 以链接至：

```bash
dcos cluster link <dcos-url>
Choose the login method and provider to enable switching to this linked cluster::
1) Provider 1
2) Provider 2
(1-2):
```

# 查看链接的群集

要查看所有链接的群集，运行 `dcos cluster list` 命令。如果群集已链接但未设置，则其状态为 `UNCONFIGURED`。如果群集已链接和附加，其状态为 `AVAILABLE`。另请参阅 [查看连接的群集](/cn/1.12/administering-clusters/multiple-clusters/cluster-connections/)。

# 删除群集链路

要删除链接，运行`dcos cluster unlink` 命令并提供已链接群集的 **名称** 或 **ID**。例如：

```bash
dcos cluster unlink <linked-cluster>
```

# 切换群集

您可以使用 CLI 或 UI 在链接的群集之间切换。使用 CLI 切换群集时，新群集将成为 CLI 的活动群集。使用 UI 切换群集时，新群集将成为您在 UI 中看到的群集。如果在 CLI 中切换群集，它不会更改 UI 中的群集；同样，在 UI 中切换，不会影响 CLI 中附加的群集。

## 从 DC/OS CLI 切换到已连接群集

运行 `dcos cluster attach` 命令并提供链接群集的名称或 ID：

```bash
dcos cluster attach <linked-cluster>
```

要运行 `dcos cluster list`，`<linked-cluster>` 的名称旁就会有一个星号。

## 从 DC/OS UI 切换到已连接群集

1. 在 DC/OS Web 界面的右上角，单击群集名称右侧的向下箭头。

    ![打开群集弹出窗口](/1.12/img/open-cluster-popup.png)

    图 1. 群集下拉列表

1. 选择 **切换群集**。

    ![swi 群集](/1.12/img/switch-cluster-1-12.png)

    图 2. 群集切换

1. 单击要切换到的群集名称。

    ![swi 链接的集群](/1.12/img/switch-linked-cluster.png)

    图 3. 切换到已连接群集

    如果您是超级用户，还可以在“已连接群集”选项卡中切换到已连接群集。

1. 选择 **群集 -> 链接群集**。

1. 在切换目标群集的最右侧单击垂直椭圆，然后选择 **切换**。

    ![swi linked cluster2](/1.12/img/switch-linked-cluster2.png)

    图 4. 切换到已连接群集



# 链接和切换群集示例

## 以超级用户操作员通过 CLI 连接群集

1. 使用 `dcos-user` 提供程序设置群集 `cluster-a` 。

    ```
    dcos cluster setup --provider=dcos-users https://cluster-a.us-west-2.elb.amazonaws.com
    ```
    响应请求您验证群集证书捆绑包中的指纹，其必须通过响应`yes`接受。
 CLI 提示提供超级用户凭证。提供凭证。

1. 使用 `dcos-user` 提供程序设置 `cluster-b`。

    ```
    dcos cluster setup --provider=dcos-users https://cluster-b.us-west-2.elb.amazonaws.com
    ```

    响应请求您验证群集证书捆绑包中的指纹，其必须通过响应`yes`接受。
 CLI 提示提供超级用户凭证。输入凭据。

1. 附加到群集 `cluster-a` 并列示。

    ```
    dcos cluster attach cluster-a
    dcos cluster list
          NAME                    CLUSTER ID                 STATUS   VERSION                                         URL
     cluster-b  34ddd64a-9301-40b1-bb6a-201ec55a0d80  AVAILABLE  1.12-dev   https://cluster-b.us-west-2.elb.amazonaws.com
    cluster-a*  584d3e8f-c5c2-4c86-b180-ff3c1f15b0d5  AVAILABLE  1.12-dev  https://cluster-a.us-west-2.elb.amazonaws.com
    ```

1. 从群集 `cluster-a` 连接到群集 `cluster-b`。

    ```
    dcos cluster link https://cluster-b.us-west-2.elb.amazonaws.com
    ```

    CLI 提示选择用于切换的登录提供程序。

    ```
    Choose the login method and provider to enable switching to this linked cluster:
    1) Log in using a standard DC/OS user account (username and password)
    2) Log in using OpenID Connect (Google IDP)
    (1-2):
    ```

1. 选择 Google IDP (2)。

    ```
    (1-2): 2
    ```

    如果群集链接成功，则无响应。

1. 附加到群集 `cluster-b`。

    ```
    $ dcos cluster attach cluster-b
    ```

1. 从群集 `cluster-b` 连接到群集 `cluster-a`。

    ```
    dcos cluster link https://cluster-a.us-west-2.elb.amazonaws.com
    ```

    CLI 提示选择用于切换的登录提供程序。

    ```
    Choose the login method and provider to enable switching to this linked cluster:
    1) Log in using a standard DC/OS user account (username and password)
    2) Log in using OpenID Connect (Google IDP)
    (1-2):
    ```

1. 选择 Google IDP (2)。

    ```
    (1-2): 2
    ```

1. 列出群集。

    ```
    dcos cluster list
          NAME                    CLUSTER ID                 STATUS   VERSION                                         URL
    cluster-b*  34ddd64a-9301-40b1-bb6a-201ec55a0d80  AVAILABLE  1.12-dev   https://cluster-b.us-west-2.elb.amazonaws.com
     cluster-a  584d3e8f-c5c2-4c86-b180-ff3c1f15b0d5  AVAILABLE  1.12-dev  https://cluster-a.us-west-2.elb.amazonaws.com
    ```


算子建立链路后，您可以使用 UI 或 CLI 在群集之间切换。

### 使用带有 Google SSO 的 Web 界面切换群集

您可以使用 Google OpenID 提供程序轻松切换到已设置的已连接群集。


1. 作为外部用户，使用 Google 凭证登录群集的 DC/OS UI `cluster-a` 。

    ![google 登录](/1.12/img/google-login.png)

    图 5. Google 登录

1. 从左上角，单击群集名称旁边的向下箭头。

    ![swi 集群](/1.12/img/switch-cluster-1-12.png)

    图 6. 切换集群

1. 单击 **切换群集**。在“链接群集”窗格中，选择群集 `cluster-b`。群集 `cluster-b`的 UI 显示。


### 使用 CLI 和 Google SSO 切换群集

您可以使用 Google OpenID 提供程序轻松切换到已设置的链接群集。

1. 列出身份验证提供程序。

    ```
    dcos auth list-providers https://cluster-a.us-west-2.elb.amazonaws.com
    PROVIDER ID    AUTHENTICATION TYPE
    dcos-services  Log in using a DC/OS service user account (username and private key)
    dcos-users     Log in using a standard DC/OS user account (username and password)
    google-idp     Log in using OpenID Connect (Google IDP)
    ```

1. 使用 Google IDP 设置群集。

    ```
    dcos cluster setup --provider=google-id https://cluster-a.us-west-2.elb.amazonaws.com
    ```

    响应要求您验证群集证书捆绑包的指纹，而且必须使用响应 `yes` 接受该响应。

1. 从浏览器复制认证令牌并粘贴到终端。

1. 列出群集。设置群集显示为“可用”并且已附加，之前链接的群集显示为“未配置”。

    ```
    dcos cluster list
          NAME                    CLUSTER ID                  STATUS     VERSION                                         URL
     cluster-b  34ddd64a-9301-40b1-bb6a-201ec55a0d80  UNCONFIGURED  1.12-dev   https://cluster-b.us-west-2.elb.amazonaws.com
    cluster-a*  584d3e8f-c5c2-4c86-b180-ff3c1f15b0d5   AVAILABLE    1.12-dev  https://cluster-a.us-west-2.elb.amazonaws.com
    ```

1. 附加到“未配置”群集。

    ```
    dcos cluster attach cluster-b
    ```

    响应请求您验证群集证书捆绑包中的指纹，其必须通过响应`yes`接受。

1. 从浏览器复制认证令牌并粘贴到终端。CLI 成功附加到群集`cluster-b`。

1. 列出群集以验证附加到 `cluster-b`。

    ```
    dcos cluster list
          NAME                    CLUSTER ID                 STATUS   VERSION                                         URL
    cluster-b*  34ddd64a-9301-40b1-bb6a-201ec55a0d80  AVAILABLE  1.12-dev   https://cluster-b.us-west-2.elb.amazonaws.com
     cluster-a  584d3e8f-c5c2-4c86-b180-ff3c1f15b0d5  AVAILABLE  1.12-dev  https://cluster-a.us-west-2.elb.amazonaws.com
    ```
