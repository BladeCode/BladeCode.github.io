---
title: Git 签名 
date: 2024-06-17 22:30:50
categories: Git
tag: [git signature]
---

![verified-commit](https://docs.github.com/assets/cb-17614/mw-1440/images/help/commits/verified-commit.webp)

通常 Push 代码到远程托管平台（GitHub，Gitlab，Gitee 等），需要提前在托管平台上传我们 Git 账户的公钥（*.pub），平台使用上传的公钥来验证身份（本地的 Git 私钥与平台上的公钥配对，以确保你有权限读写该仓库），该验证只会在 Push 时进行检查

<!-- more -->

## 为什么要签名

虽然对 Push 做了检查，但依然不够安全，因为任何拥有该仓库权限的人，都可以在 commit/tag 时使用 `git config user.name "假的用户名"`, `git config user.email "假的邮箱地址"` 命令来伪造提交者用户信息，这样我们根本无法追溯提交者的身份，所以我们需要给 commit/tag 签名，签名的目的是确保提交的代码在传输和存储过程中没有被篡改，并验证提交者的身份，同时也保证代码的可追溯性

commit 签名是在本地，在使用 `git commit` 命令时进行签名，push 时会将你的签名信息，原封不动 push 到远程仓库

commit 签名只是用于验证这条 commit 来自于你本人，与是否有权限操作远程仓库无关

## 如何签名

可以使用 GPG、SSH 或 S/MIME，可以在本地对 commit/tag 进行签名。 这些 commit/tag 在 GitHub 上标示为已验证，便于其他人信任更改来自可信的来源

### SSH、GPG、S/MIME 区别

- SSH 签名是最容易生成的，甚至可以将现有身份验证密钥上传到 GitHub 以用作签名密钥
- 生成 GPG 签名比生成 SSH 密钥复杂，但 GPG 具有 SSH 没有的功能，GPG 密钥可以在不使用时过期或撤销
- 较大型组织的环境中通常需要 S/MIME 签名

### 👍 SSH（常用）

> SSH 签名验证需 <font color=red>Git 2.3.4</font> 及以上版本

#### 检查现有 SSH 密钥

```bash
# 查看本地 ~/.ssh 路径现有密钥
ls -al ~/.ssh
# 检查输出的列表
```

检查已有密钥列表已有 RSA 密钥，如果你已经有 **SHA-2 算法生成 RSA 密钥** 那么你可以 <font color=red>跳过</font> `生产新 SSH 密钥`，如果没有，为了安全，还是建议重新生成 SHA-2 算法生成 RSA 密钥

#### 生产新 SSH 密钥

{% note info %}

1. 2022.03.15
   - 在该日期 GitHub 删除旧的、不安全的密钥类型来提高安全性
   - 自该日期起，<font color=red>不再支持</font> **DSA 密钥** (ssh-dss)。 <font color=red>无法</font> 在 github.com 上向个人帐户添加新的 **DSA 密钥**
2. 2021.11.02
   - 在该日期 **之前** 带有 valid_after 的 **RSA 密钥** (ssh-rsa) 可以继续使用任何签名算法
   - 在该日期 **之后** 生成的 RSA 密钥 <font color=red>必须</font> 使用 **SHA-2 签名算法**。 一些较旧的客户端可能需要升级才能使用 SHA-2 签名

{% endnote %}

在生成 SSH 密钥时，我们可以给 SSH 密钥添加密码，也可以不添加密码，这里根据需要选择是否添加密码

![git-ssh-keygen](https://res.cloudinary.com/incoder/image/upload/v1722773384/blog/git-ssh-keygen.png)

1. 执行 `ssh-keygen -t ed25519 -C "Jerry.x@outlook.com"` 命令，这里的邮箱换成你的 GitHub 邮箱
2. 确认密钥存放位置
   - 如果不需调整（默认：`/User/blade/.ssh/id_ed25519`），可 Enter 键进入下一步
   - 这里我重命名了密钥 `/User/blade/.ssh/id_ed25519_test`
3. 给密钥设置密码
   - 如果无需设置，可 Enter 键进入下一步
   - 如果需设置，输入密码即可
4. 确认输入的密钥密码，和上一步输入内容一致
5. 提示生成的密钥存放位置和指纹信息

{% note primary 如果密钥 **设置了密码**，需要将 SSH 密钥添加到 ssh-agent %}

在向 ssh-agent 添加新的 SSH 密钥管理密钥前，应该检查现有 SSH 密钥并生成新的 SSH 密钥

{% tabs Sixth unique name %}

<!-- tab Windows@fa-brands fa-windows -->

> 如果已安装 [GitHub Desktop](https://github.com/apps/desktop)，可使用它克隆存储库，而无需处理 SSH 密钥

1. 在新的“管理员提升”__ PowerShell 窗口中，确保 ssh-agent 正在运行。 可以使用“使用 SSH 密钥密码”中的“自动启动 ssh agent”说明，或者手动启动它

    ```bash
    # start the ssh-agent in the background
    Get-Service -Name ssh-agent | Set-Service -StartupType Manual
    Start-Service ssh-agent
    ```

2. 在无提升权限的终端窗口中，将 SSH 私钥添加到 ssh-agent。 如果使用其他名称创建了密钥，或要添加具有其他名称的现有密钥，请将命令中的 id_ed25519 替换为私钥文件的名称

    ```bash
    ssh-add c:/Users/YOU/.ssh/id_ed25519
    ```

<!-- endtab -->

<!-- tab macOS@fa-brands fa-apple -->

> 将 SSH 密钥添加到该代理时，应使用默认的 macOS ssh-add 命令，而不是使用通过 macports、homebrew 或某些其他外部来源安装的应用程序

1. 在后台启动 ssh 代理

    ```bash
    $ eval "$(ssh-agent -s)"
    > Agent pid 59566
    ```

    根据您的环境，您可能需要使用不同的命令。 例如，在启动 `ssh-agent` 之前，你可能需要通过运行 `sudo -s -H` 根访问，或者可能需要使用 `exec ssh-agent bash` 或 `exec ssh-agent zsh` 运行 `ssh-agent`

2. 如果你使用的是 macOS Sierra **10.12.2** 或更高版本，则需要修改 `~/.ssh/config` 文件以自动将密钥加载到 `ssh-agent` 中并在密钥链中存储密码
   - 检查你的 `~/.ssh/config` 文件是否在默认位置
   - 如果文件不存在，请创建该文件
   - 打开你的 `~/.ssh/config` 文件，然后修改文件以包含以下行。 如果您的 SSH 密钥文件与示例代码具有不同的名称或路径，请修改文件名或路径以匹配您当前的设置

        ```bash
        Host github.com
          # 如果看到了 Bad configuration option: usekeychain 错误，
          # 取消下一行注释，使用 IgnoreUnknown 配置
          # IgnoreUnknown UseKeychain
          AddKeysToAgent yes
          # 如果你选择不向密钥添加密码，应该省略 UseKeychain 行
          UseKeychain yes
          IdentityFile ~/.ssh/id_ed25519
        ```

3. 将 SSH 私钥添加到 `ssh-agent` 并将密码存储在密钥链中。 如果使用其他名称创建了密钥，或要添加具有其他名称的现有密钥，请将命令中的 **id_ed25519** 替换为私钥文件的名称

    ```bash
    ssh-add --apple-use-keychain ~/.ssh/id_ed25519
    ```

{% note warning no-icon 注意 %}

1. 当你将 SSH 密钥添加到 ssh-agent 时，`--apple-use-keychain` 选项会将密码存储在你的密钥链中。 如果选择不向密钥添加密码，请运行命令，而不使用 `--apple-use-keychain` 选项
2. 选项 `--apple-use-keychain` 位于 Apple 的 ssh-add 标准版本中。 在 Monterey (12.0) 之前的 macOS 版本中，`--apple-use-keychain` 和 `--apple-load-keychain` 标志分别使用语法 {% label @-K %} 和 {% label @-A %}
3. 如果您没有安装 Apple 的 ssh-add 标准版本，可能会收到错误消息。 有关详细信息，请参阅 “[错误：ssh-add：非法选项 -- apple-use-keychain](https://docs.github.com/zh/authentication/troubleshooting-ssh/error-ssh-add-illegal-option----apple-use-keychain)”
4. 如果系统继续提示你输入密码，则可能需要将命令添加到 `~/.zshrc` 文件（或 bash 对应的 `~/.bashrc` 文件）

{% endnote %}

<!-- endtab -->

<!-- tab Linux@fa-brands fa-linux -->

1. 在后台启动 ssh 代理

    ```bash
    $ eval "$(ssh-agent -s)"
    > Agent pid 59566
    ```

    根据您的环境，您可能需要使用不同的命令。 例如，在启动 `ssh-agent` 之前，你可能需要通过运行 `sudo -s -H` 根访问，或者可能需要使用 `exec ssh-agent bash` 或 `exec ssh-agent zsh` 运行 `ssh-agent`

2. 将 SSH 私钥添加到 ssh-agent

   如果使用其他名称创建了密钥，或要添加具有其他名称的现有密钥，请将命令中的 **id_ed25519** 替换为私钥文件的名称

   ```bash
   ssh-add ~/.ssh/id_ed25519
   ```

<!-- endtab -->
{% endtabs %}
{% endnote %}

除了上述的方式生成密钥，还有 [为硬件安全密钥生成新的 SSH 密钥](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#generating-a-new-ssh-key-for-a-hardware-security-key) 方式，这里不做说明，可以移步官方文档查看

#### 将 SSH 密钥添加到 GitHub 账户

![github-ssh-add](https://res.cloudinary.com/incoder/image/upload/v1722781365/blog/github-ssh-add.png)

> 其他更多设置可参考官方文档 [新增 SSH 密钥到 GitHub 帐户](https://docs.github.com/zh/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

#### 将签名密钥告诉 Git

{% note warning %}

- 如需全局配置：添加 `--global` 参数
- 如果你有多个 Git 账号，推荐 <font color=red>按照不同的账号分别进行配置</font>，具体可参考 [Git 多账号配置](git-account.md#添加配置文件) 这篇文章

{% endnote %}

```bash
# 1. 配置 Git 使用 SSH 对提交和标记签名
git config gpg.format ssh
# 2. 配置指定 SSH 签名密钥
#    将 ~/.ssh/id_ed25519 替换为要使用的公钥路径
git config user.signingkey ~/.ssh/id_ed25519.pub
# commit 开启自动签名
git config commit.gpgsign true
# tag 开启自动签名
git config tag.gpgsign true
```

#### 配置可信公钥列表

```bash
mkdir -p ~/.config/git
touch ~/.config/git/allowed_signers
# 将指定的密钥文件（~/.ssh/id_ed25519）内容复制到 allowed_signers 文件中
# 如果是继续追加, 将 > 替换为 >>
cat ~/.ssh/id_ed25519.pub > ~/.config/git/allowed_signers
# 配置可信公钥
git config gpg.ssh.allowedSignersFile "~/.config/git/allowed_signers"
```

#### 对 commit 签名

```bash
# 1. 当本地分支中的提交更改时，请将 -S 标志添加到 git commit 命令
git commit -S -m "YOUR_COMMIT_MESSAGE"
# 2. 在本地完成创建提交后，将其推送到 GitHub 上的远程仓库
git push
```

#### 对 tag 签名

> 注意：如果 Git 客户端配置为默认对提交进行签名，GitHub Desktop 仅支持提交签名

```bash
# 1. 若要对标记进行签名，请将 -s 添加到 git tag 命令
git tag -s MYTAG
# 2. 通过运行 git tag -v [tag-name] 验证已签名的标记
git tag -v MYTAG
```

### GPG

> 具体实践可参考官方文档 [GPG 提交签名验证](https://docs.github.com/zh/authentication/managing-commit-signature-verification/about-commit-signature-verification#gpg-commit-signature-verification)

### S/MIME

> S/MIME 签名验证需 Git 2.19 及以上版本

由于 S/MIME 用的比较少，这里就不做具体的演示，可参考 [官方文档](https://docs.github.com/zh/authentication/managing-commit-signature-verification/about-commit-signature-verification#smime-commit-signature-verification)

## 验证签名

```bash
# 查看一下本地签名信息
# 正常情况，在 commit 提交号下
# good "git" signature for $(email) with $(publicKey)
git log --show-signature
```

## 问题

### 验证签名提示异常

{% tabs signature unique name %}

<!-- tab No signature -->

No signature:

- 表示 Git 不知道要信任哪些 SSH 密钥
- 解决方法：配置[可信公钥列表](#配置可信公钥列表)

![no-signature](https://res.cloudinary.com/incoder/image/upload/v1723359614/blog/git-no-signature.png)

<!-- endtab -->

<!-- tab No principal matched -->

No principal matched

![no-principal](https://res.cloudinary.com/incoder/image/upload/v1723359614/blog/git-no-principal.png)

<!-- endtab -->

<!-- tab invalid key -->

invalid key

![invalid-key](https://res.cloudinary.com/incoder/image/upload/v1723359614/blog/git-invalid-key.png)

<!-- endtab -->

{% endtabs %}

### 如何给现有密钥更新密码

通过输入以下命令，您可以 <font color="red">更改</font> **现有私钥** 的密码而无需重新生成密钥对

```bash
# 修改 id_ed25519 密钥密码
$ ssh-keygen -p -f ~/.ssh/id_ed25519
> Enter old passphrase: [Type old passphrase]
> Key has comment 'your_email@example.com'
> Enter new passphrase (empty for no passphrase): [Type new passphrase]
> Enter same passphrase again: [Repeat the new passphrase]
> Your identification has been saved with the new passphrase.
```

## 参考

1. [Git 提交使用 SSH 签名和 GPG 签名验证](https://piaohua.github.io/post/git/20230624-git-ssh-gpg/)
2. [SSH 提交签名验证](https://lruihao.cn/posts/ssh-sign/)
3. [GitHub commit 签名指南](https://ayk.moe/articles/commit-signature-guide/index.html)
4. [维护代码的尊严：GPG签名让你的Git commit不再裸奔](https://juejin.cn/post/7268593569782300727)
