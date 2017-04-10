---
title: "Azure Linux 仮想マシンで SSHD を構成する | Microsoft Docs"
description: "セキュリティのベスト プラクティスに対応し、Azure Linux 仮想マシンへの SSH 接続をロックダウンするように SSHD を構成します。"
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 08499c4242fdc59ef932d6b8f2e8442e5cdc55b2
ms.openlocfilehash: a606f06de55b1db4392d066a3e1d026eb3b54d74


---

# <a name="configure-sshd-on-azure-linux-vms"></a>Azure Linux VM で SSHD を構成する

この記事では、Linux で SSH サーバーをロックダウンする方法、ベスト プラクティスに従ったセキュリティを提供する方法、パスワードの代わりに SSH キーを使用して SSH ログイン プロセスを迅速化する方法について説明します。  SSHD をさらにロックダウンするために、ルート ユーザーによるログインの無効化、承認済みグループ リストを使用したログインを許可するユーザーの制限、SSH プロトコル バージョン 1 の無効化、キーの最小ビット数の設定、アイドル状態のユーザーの自動ログアウトの構成を行います。  この記事の手順を実行するには、Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/)) と、[SSH 公開キーおよび秘密キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)が必要です。

## <a name="quick-commands"></a>クイック コマンド

次の設定で `/etc/ssh/sshd_config` を構成します。

### <a name="disable-password-logins"></a>パスワード ログインを無効にする

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>ルート ユーザーによるログインを無効にする

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>許可されたグループ リスト

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>許可されたユーザー リスト

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>SSH プロトコル バージョン 1 を無効にする

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>キーの最小ビット数

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>アイドル状態のユーザーを切断する

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

SSHD は Linux VM で実行されている SSH Server です。  SSH は、MacBook および Linux ワークステーションのシェルまたは Windows の Bash から実行するクライアントです。  SSH は、ワークステーションと、VPN (仮想プライベート ネットワーク) にも SSH 接続する Linux VM 間の通信のセキュリティ保護と暗号化に使用されるプロトコルでもあります。

この記事では、チュートリアル全体を通して Linux VM への&1; つのログインを常に開いておくことが非常に重要です。  SSH 接続が確立されると、ウィンドウを閉じない限り、開いているセッションとして接続が保持されます。  ログインしているターミナルが&1; つあれば、重大な変更を行う場合に、ロックアウトされることなく SSHD サービスに変更を加えることができます。  SSHD 構成の破損によって Linux VM からロックアウトされた場合、Azure では [Azure VM アクセス拡張機能](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を使用して破損した SSHD 構成をリセットできます。

そのため、2 つのターミナルを開き、その両方から Linux VM に SSH 接続します。  一方のターミナルを使用して SSHD 構成ファイルを変更し、SSHD サービスを再起動します。  サービスが再起動したら、もう一方のターミナルを使用して変更をテストします。  SSH パスワードを無効にして SSH キーだけに依存するようにするので、SSH キーが正しくない場合、VM への接続を閉じると、VM は永続的にロックされて誰もログインできなくなり、削除して再作成することが必要になります。

## <a name="disable-password-logins"></a>パスワード ログインを無効にする

Linux VM をセキュリティで保護する最も簡単な方法は、パスワード ログインを無効にすることです。  パスワード ログインが有効になっていると、Web をクロールするボットが、SSH を使用してブルート フォース方式で Linux VM のパスワードの推測を即座に開始します。  パスワード ログインを完全に無効にすることで、SSH サーバーはパスワード ログイン試行をすべて無視できます。

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>ルート ユーザーによるログインを無効にする

Linux のベスト プラクティスに従い、`root` ユーザーが SSH または `sudo su` を使用してログインできないようにします。  ルート レベルのアクセス許可を必要とするすべてのコマンドは、常に `sudo` コマンドを介して実行する必要があります。このコマンドは、今後の監査用にすべてのアクションをログに記録します。  `root` ユーザーによる SSH 経由のログインの無効化は、許可されているユーザーだけに SSH 接続を許可する、セキュリティのベスト プラクティスです。

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>許可されたグループ リスト

SSH では、SSH 経由のログインを許可または禁止するユーザーとグループを制限できます。  この機能では、リストを使用して特定のユーザーとグループによるログインを承認または拒否します。  wheel グループを `AllowGroups` リストに設定すると、SSH 経由の承認済みログインが、wheel グループに属するユーザー アカウントだけに制限されます。

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>許可されたユーザー リスト

SSH ログインをユーザーだけに制限することは、`AllowGroups` と同じタスクを実現するより限定的な方法です。  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>SSH プロトコル バージョン 1 を無効にする

SSH プロトコル バージョン 1 は安全ではないので、無効にする必要があります。  SSH プロトコル バージョン 2 は、サーバーに SSH 接続する安全な方法を提供する最新バージョンです。  SSH バージョン 1 を無効にすると、SSH バージョン 1 を使用して SSH サーバーとの接続を確立しようとしている SSH クライアントが拒否されます。  SSH バージョン 2 の接続だけが、SSH サーバーとの接続をネゴシエートできます。

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>キーの最小ビット数

セキュリティのベスト プラクティスに従って、SSH パスワード ログインを無効にしたら、SSH サーバーでの認証に SSH キーだけを使用できるようにします。  これらの SSH キーは、さまざまな長さ (ビット単位) のキーを使用して作成できます。  ベスト プラクティスでは、長さが 2048 ビットのキーが、許容されるキーの最小強度とされています。  理論的には、2048 ビット未満のキーは破損している可能性があります。  `ServerKeyBits` を `2048` に設定すると、2048 ビット以上のキーを使用する接続は許可され、2048 ビット未満のキーを使用する接続は拒否されます。

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>アイドル状態のユーザーを切断する

SSH では、開いている接続が一定期間 (秒単位) を過ぎてもアイドル状態のままであるユーザーを切断できます。  アクティブなユーザーの開いているセッションだけを維持することで、Linux VM の公開を制限できます。

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>SSHD を再起動する

`/etc/ssh/sshd_config` の設定を有効にするには、SSH サーバーを再起動する SSHD プロセスを再起動します。  SSH サーバーの再起動に使用するターミナル ウィンドウは開いたままになり、開いている SSH セッションが失われることはありません。  新しい SSH サーバー設定をテストするには、別のターミナル ウィンドウまたはタブを使用します。  別のターミナルを使用して SSH 接続をテストすると、最初のターミナルで `/etc/ssh/sshd_config` に戻って変更を加えることができるため、SSHD の重大な変更によってロックアウトされることはありません。  

### <a name="on-redhat-centos-and-fedora"></a>RedHat、CentOS、Fedora の場合

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>Debian、Ubuntu の場合

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>Azure の reset-access を使用して SSHD をリセットする

SSHD 構成の重大な変更によってロックアウトされた場合は、Azure VM アクセス拡張機能を使用して SSHD 構成を元の構成にリセットできます。

例の名前を実際の名前に置き換えてください。

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>Fail2ban をインストールする

オープン ソース アプリの Fail2ban をインストールしてセットアップすることを強くお勧めします。Fail2ban は、ブルート フォースを使用して繰り返される、SSH 経由での Linux VM へのログイン試行をブロックします。  Fail2ban では、繰り返し失敗した SSH 経由のログイン試行を記録し、試行の実行元の IP アドレスをブロックするファイアウォール規則を作成します。

* [Fail2ban のホームページ](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>次のステップ

Linux VM で SSH サーバーを構成し、サーバーをロックダウンできました。従うことができるセキュリティのベスト プラクティスはほかにもあります。  

* [VMAccess 拡張機能を使用して、Azure Linux VM 上のユーザー、SSH を管理し、ディスクをチェックまたは修復する](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Azure CLI を使って Linux VM のディスクを暗号化する](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Azure Resource Manager テンプレートでのアクセスとセキュリティ](virtual-machines-linux-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


