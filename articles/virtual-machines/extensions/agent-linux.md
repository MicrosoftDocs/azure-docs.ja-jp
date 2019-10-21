---
title: Azure Linux VM エージェントの概要 | Microsoft Docs
description: Azure ファブリック コントローラーと仮想マシンとの相互動作を管理するために、Linux エージェント (waagent) をインストールして構成する方法について説明します。
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e8bc28c7454296f32dda09894ad3dca2f4fae99b
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71169165"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Azure Linux エージェントの理解と使用

Microsoft Azure Linux エージェント (waagent) は、Linux と FreeBSD のプロビジョニング、および仮想マシンと Azure ファブリック コントローラーとの相互動作を管理します。 Azure には､プロビジョニング機能を提供する Linux エージェントに加えて､一部 Linux OS 用に cloud-init を使用するオプションも用意されています｡ Linux エージェントは､Linux および FreeBSD IaaS デプロイ用の次の機能を提供します｡

> [!NOTE]
> 詳細については、 [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md) をご覧ください。
> 
> 

* **イメージのプロビジョニング**
  
  * ユーザー アカウントの作成
  * SSH 認証の種類の構成
  * SSH 公開キーおよびキー ペアのデプロイ
  * ホスト名の設定
  * プラットフォーム DNS へのホスト名の発行
  * プラットフォームへの SSH ホスト キーの指紋のレポート
  * リソース ディスクの管理
  * リソース ディスクのフォーマットとマウント
  * スワップ領域の構成
* **ネットワーク**
  
  * プラットフォーム DHCP サーバーとの互換性を向上させるためのルートの管理
  * ネットワーク インターフェイス名の安定性の保持
* **カーネル**
  
  * 仮想 NUMA (`2.6.37` 未満のカーネルでは無効) の構成
  * /dev/random の Hyper-V エントロピの使用
  * root デバイス (リモート デバイス) の SCSI タイムアウトの構成
* **診断**
  
  * コンソールからシリアル ポートへのリダイレクト
* **SCVMM のデプロイ**
  
  * System Center Virtual Machine Manager 2012 R2 環境で実行されているときに Linux 用の VMM エージェントを検出およびブートストラップ
* **VM 拡張機能**
  
  * Microsoft やパートナーによって作成されたコンポーネントを Linux VM (IaaS) に挿入し、ソフトウェアおよび構成を自動化
  * [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions) の VM 拡張機能のリファレンス実装

## <a name="communication"></a>Communication
プラットフォームからエージェントへの情報の流れは 2 つのチャンネルを経由します。

* 起動時に接続される IaaS デプロイ用 DVD。 この DVD に、OVF に準拠している構成ファイルが収録されており、このファイルに、実際の SSH キー ペア以外のすべてのプロビジョニング情報が保存されています。
* デプロイとトポロジの構成を取得するために使用する REST API を公開する TCP エンドポイント。

## <a name="requirements"></a>必要条件
次のシステムがテスト済みで、Azure Linux エージェントで機能することがわかっています。

> [!NOTE]
> この一覧は、Microsoft Azure Platform でサポートされるシステムの公式の一覧とは異なる場合があります (Microsoft Azure Platform でサポートされるシステムの一覧: [https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216))
> 
> 

* CoreOS
* CentOS 6.3+
* Red Hat Enterprise Linux 6.7+
* Debian 7.0+
* Ubuntu 12.04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6.4+

サポートされるその他のシステム:

* FreeBSD 10+ (Azure Linux エージェント v2.0.10+)

Linux エージェントが正しく機能するには、次の該当するシステム パッケージが必要です。

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* ファイルシステム ユーティリティ: sfdisk、fdisk、mkfs、parted
* パスワード ツール: chpasswd、sudo
* テキスト処理ツール: sed、grep
* ネットワーク ツール: ip-route
* UDF ファイルシステムのマウントのカーネル サポート。

## <a name="installation"></a>インストール
使用しているディストリビューションのパッケージのリポジトリから RPM または DEB パッケージを使用してインストールする方法は、Azure Linux エージェントのインストールおよびアップグレードとしてお勧めする方法です。 すべての[動作保証済みディストリビューション プロバイダー](../linux/endorsed-distros.md)Azure Linux エージェント パッケージをイメージとリポジトリに統合します。

ソースからカスタムの場所へのインストールやプレフィックスなどの高度なインストール オプションについては、[GitHub の Azure Linux Agent リポジトリ](https://github.com/Azure/WALinuxAgent)にあるドキュメントを参照してください。

## <a name="command-line-options"></a>コマンド ライン オプション
### <a name="flags"></a>フラグ
* verbose: 指定したコマンドのメッセージの詳細度を上げます
* force: 一部のコマンドの対話形式の確認をスキップします

### <a name="commands"></a>command
* help: サポートされているコマンドとフラグを一覧表示します。
* deprovision: システムをクリーンアップし、再プロビジョニングに適した状態にしようとします。 次の項目を削除します｡
  
  * すべての SSH ホスト キー (構成ファイルで Provisioning.RegenerateSshHostKeyPair が 'y' の場合)
  * /etc/resolv.conf 内のネームサーバー構成
  * /etc/shadow の root パスワード (構成ファイルで Provisioning.DeleteRootPassword が 'y' の場合)
  * キャッシュされた DHCP クライアントのリース
  * ホスト名を localhost.localdomain にリセット

> [!WARNING]
> プロビジョニング解除により、イメージからすべての機密情報が削除され、イメージが再配布に適した状態になることが保証されるわけではありません。
> 
> 

* deprovision+user: -deprovision の場合のすべての対象 (上記参照) を実行するほか、前回プロビジョニングされたユーザー アカウント (/var/lib/waagent から取得) および関連付けられたデータも削除します。 このパラメーターは、Azure で前回プロビジョニングされたイメージのプロビジョニングを解除するため、取得して再利用できます。
* version: waagent のバージョンを表示します
* serialconsole: GRUB で ttyS0 (最初のシリアル ポート) がブート コンソールになるように構成します。 これにより、カーネルの起動ログがシリアル ポートに送信され、デバッグに使用できるようになります。
* daemon: プラットフォームとの相互動作を管理するデーモンとして waagent を実行します。 この引数には waagent init スクリプト内で waagent が指定されます。
* start: バックグラウンド プロセスとして waagent を実行します

## <a name="configuration"></a>構成
構成ファイル (/etc/waagent.conf) を使用して waagent の動作を制御します。 以下は構成ファイルの例です｡

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

以下では､これらのさまざまな構成オプションについて説明します｡ 構成オプションには、ブール、文字列、整数の 3 つの型があります。 ブール型の構成オプションは "y" または "n" として指定できます。 特別なキーワード "None" は、次に詳述しているように、一部の文字列型の構成オプションに使用できます。

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
エージェントのプロビジョニング機能を有効または無効にすることができます。 有効な値は "y" または "n" です。 プロビジョニングを無効にした場合、イメージ上の SSH ホストとユーザー キーが保持され、Azure プロビジョニング API で指定した構成はすべて無視されます。

> [!NOTE]
> cloud-init を使用してプロビジョニング タスクを実行する Ubuntu Cloud Image では、`Provisioning.Enabled` パラメーターが既定で "n" に設定されています。
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
設定した場合、/etc/shadow 内の root パスワードがプロビジョニング プロセス中に消去されます。

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
設定した場合は、/etc/ssh/ からプロビジョニング プロセス中にすべての SSH ホスト キー ペア (ecdsa、dsa、rsa) が削除されます。 1 つの新しいキー ペアが生成されます。

新しいキー ペアの暗号化の種類は Provisioning.SshHostKeyPairType オプションで構成できます。 一部ディストリビューションでは、不明な暗号化の種類があると､SSH デーモンの再起動時 (再起動プロセス後など)、そのための SSH キー ペアが作成し直されます｡

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
仮想マシンの SSH デーモンによってサポートされている暗号化アルゴリズムの種類を設定できます。 一般的にサポートされている値は "rsa"、"dsa"、"ecdsa" です。 Windows では､ "ecdsa" は "putty.exe" によって サポートされません｡ このため、Windows で putty.exe を使用して Linux デプロイに接続する場合は、"rsa" または "dsa" を利用します｡

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
設定した場合、waagent は Linux 仮想マシンでホスト名の変更があるかどうか ("hostname" コマンドによって返される値) を監視し、変更がある場合は､イメージ上のネットワーク構成を自動的に更新して反映します。 DNS サーバーに名前の変更をプッシュするため、仮想マシンでネットワークが再起動されます。 これによりインターネット接続は短時間失われます｡

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
設定した場合､waagent は Base64 の CustomData をデコードします｡

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
設定した場合、waagent はプロビジョニング後に CustomData を実行します。

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
このオプションでは sys ユーザーのパスワードをリセットします。既定値は無効です。

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
パスワード ハッシュの生成時に、crypt によって使用されるアルゴリズム。  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
パスワード ハッシュの生成時に使用されるランダム salt の長さ。

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
このオプションが設定されていて､"ResourceDisk.Filesystem" でユーザーが要求したファイル システムの種類が "ntfs" 以外の場合は、プラットフォームが提供するリソース ディスクが waagent によってフォーマットされてマウントされます。 このディスクでは､Linux (83) タイプのパーティションが 1 つ使用可能になります。 正常にマウントできる場合､ディスクはフォーマットされません｡

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
リソース ディスクのファイルシステムの種類を指定します。 サポートされている値は Linux ディストリビューションによって異なります。 文字列が X の場合、mkfs.X は Linux イメージ上に存在する必要があります。 SLES 11 イメージでは通常は 'ext3' を使用する必要があります。 FreeBSD イメージではここで 'ufs2' を使用する必要があります。

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
リソース ディスクがマウントされるパスを指定します。 リソース ディスクは *一時* ディスクであるため、VM のプロビジョニングが解除されると空にされることがあります｡

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
mount -o コマンドに渡されるディスク マウント オプションを指定します。 これは値のコンマ区切りリストです。たとえば、 'nodev,nosuid' などです。 詳細については mount(8) を参照してください。

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
設定した場合、スワップ ファイル (/swapfile) がリソース ディスク上に作成され、システムのスワップ領域に追加されます。

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
スワップ ファイルのサイズを MB 単位で指定します。

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
設定した場合、ログの詳細度が上がります。 Waagent は /var/log/waagent.log にログを記録し、システムの logrotate 機能を利用してログを交代で利用します。

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
設定した場合、エージェントは、土台のハードウェアのファームウェアのバージョンに一致する RDMA カーネル ドライバーをインストールし、読み込もうとします。

**OS.RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
OS ディスクおよびデータ ドライブの SCSI タイムアウトを秒単位で構成します。 設定しない場合、システムの既定値が使用されます。

**OS.OpensslPath:**  
```
Type: String  
Default: None
```
暗号化処理に使用する openssl バイナリの代替パスを指定できます。

**HttpProxy.Host、HttpProxy.Port**  
```
Type: String  
Default: None
```
設定した場合、エージェントはこのプロキシ サーバーを使用して、インターネットにアクセスします。 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
目標状態の処理の自動更新を有効または無効にします。規定値は有効です。



## <a name="ubuntu-cloud-images"></a>Ubuntu Cloud Image
Ubuntu Cloud Image では [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) を使用して、通常は Azure Linux エージェントによって管理される、さまざまな構成タスクを実行します。 相違点は以下の通りです｡

* **Provisioning.Enabled** が既定で "n" に設定されています。
* 次の構成パラメーターは、cloud-init を使用してリソース ディスクとスワップ領域を管理する Ubuntu Cloud Image に影響を与えません。
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* 詳しくは､プロビジョニング中に Ubuntu Cloud Image にリソース ディスク マウント ポイントとスワップ領域を構成するための次のリソースを参照してください。
  
  * [Ubuntu Wiki: スワップ パーティションの構成](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Azure の仮想マシンにカスタム データを挿入する](../windows/classic/inject-custom-data.md)

