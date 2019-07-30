---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Microsoft Azure のオンデマンド Red Hat Enterprise Linux インスタンス用の Red Hat Update Infrastructure について説明します
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: 2add03d68b00fb18fb4323a6acd04480b3770c1c
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708417"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure
 クラウド プロバイダー (Azure など) は、[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) を使用して、Red Hat でホストされているリポジトリのコンテンツのミラーリング、Azure 固有のコンテンツを使用したカスタム リポジトリの作成、およびエンド ユーザーの VM での使用を実行できます。

Red Hat Enterprise Linux (RHEL) 従量課金制 (PAYG) イメージには、Azure RHUI にアクセスするための構成が事前に設定されています。 追加の構成は必要ありません。 最新の更新プログラムを取得するには、RHEL インスタンスの準備ができてから `sudo yum update` を実行します。 このサービスは、RHEL PAYG ソフトウェア料金の一部として含まれています。

Azure での RHEL イメージに関する追加情報 (公開および保持ポリシーを含む) は[ここ](./rhel-images.md)で入手できます。

すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。

## <a name="important-information-about-azure-rhui"></a>Azure RHUI に関する重要な情報
* Azure RHUI は、Azure で作成されるすべての RHEL PAYG VM をサポートする更新インフラストラクチャです。 これは、お使いの PAYG RHEL VM を Subscription Manager や Satellite、またはその他の更新ソースに登録することを妨げるものではありませんが、PAYG VM でそれを行うと、間接的に二重請求が発生します。 詳しくは、以下の点を参照してください。
* Azure でホストされている RHUI へのアクセスは、RHEL PAYG イメージの料金に含まれています。 Azure でホストされている RHUI から PAYG RHEL VM の登録を解除した場合は、仮想マシンが Bring-Your-Own-License (BYOL: ライセンス持ち込み) タイプの VM に変換されません。 そのため、別の更新ソースに同じ VM を登録した場合は、_間接_料金が二重に発生する可能性があります。 1 つ目は Azure RHEL ソフトウェア料金に対するものです。 2 つ目は、以前に購入した Red Hat のサブスクリプションに対するものです。 Azure でホストされている RHUI 以外の更新インフラストラクチャを常に使用する必要がある場合は、[RHEL BYOS イメージ](https://aka.ms/rhel-byos)を使用するための登録を検討してください。
* RHUI の既定の動作では、`sudo yum update` を実行したときに RHEL VM は最新のマイナー バージョンにアップグレードされます。

    たとえば、RHEL 7.4 PAYG イメージから VM をプロビジョニングして `sudo yum update` を実行した場合は、RHEL 7.6 VM (RHEL7 ファミリ内の最新のマイナー バージョン) にアップグレードされます。

    この動作を回避するには、[拡張更新サポート チャネル](#rhel-eus-and-version-locking-rhel-vms)に切り替えるか、[Azure 用の Red Hat ベースの仮想マシンの作成とアップロード](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の説明に従って、独自のイメージを作成することができます。 独自のイメージを構築する場合は、それを別の更新インフラストラクチャ ([直接 Red Hat のコンテンツ配信サーバー](https://access.redhat.com/solutions/253273)または [Red Hat Satellite サーバー](https://access.redhat.com/products/red-hat-satellite)) に接続する必要があります。



* Azure での RHEL SAP PAYG イメージ (RHEL for SAP、RHEL for SAP HANA、および RHEL for SAP Business Applications) は、SAP 認定に必要な特定の RHEL マイナー バージョンのままになっている専用の RHUI チャネルに接続されます。

* Azure でホストされている RHUI へのアクセスは、[データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。 すべての VM トラフィックをオンプレミスのネットワーク インフラストラクチャ経由でプロキシ処理している場合は、RHEL PAYG VM 用のユーザー定義のルートを設定して Azure RHUI にアクセスしなければならない場合があります。

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS およびバージョン固定の RHEL VM
一部の顧客は、RHEL VM を特定の RHEL マイナー リリースに固定したいと考える可能性があります。 リポジトリを Extended Update Support リポジトリを指すように更新することによって、RHEL VM を特定のマイナー バージョンに固定できます。 また、EUS バージョン ロック操作を取り消すこともできます。

>[!NOTE]
> EUS は、RHEL Extras ではサポートされていません。 つまり、通常 RHEL Extras チャネルから利用できるパッケージをインストールする場合、EUS を使用している間はそれを実行できないことになります。 Red Hat Extras の成果物ライフサイクルの詳細については、[こちら](https://access.redhat.com/support/policy/updates/extras/)をご覧ください。

本書の執筆時点では、RHEL <= 7.3 の EUS サポートは終了しています。 詳細については、[Red Hat ドキュメント](https://access.redhat.com/support/policy/updates/errata/)の「Red Hat Enterprise Linux の長期サポート アドオン」セクションを参照してください。
* RHEL 7.4 EUS サポートは、2019 年 8 月 31 日に終了します
* RHEL 7.5 EUS サポートは、2020 年 4 月 30 日に終了します
* RHEL 7.6 EUS サポートは、2020 年 10 月 31 日に終了します

### <a name="switch-a-rhel-vm-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM を EUS に切り替える (特定のマイナー バージョンにバージョン ロックする)
RHEL VM を特定のマイナー リリースに固定するには、次の手順を使用します (ルートとして実行)。

>[!NOTE]
> このことは、EUS が利用できるバージョンの RHEL にのみ当てはまります。 この記事の作成時点で、これに該当するのは RHEL 7.2-7.6 です。 詳しくは、[Red Hat Enterprise Linux のライフ サイクル](https://access.redhat.com/support/policy/updates/errata)に関するページをご覧ください。

1. EUS 以外のリポジトリを無効にします。
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS リポジトリを追加します。
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. releasever 変数をロックします (ルートとして実行):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上の命令は、RHEL マイナー リリースを現在のマイナー リリースに固定します。 アップグレードに固定しており、最新ではない将来のマイナー リリースに固定する場合は、特定のマイナー リリースを入力します。 たとえば、`echo 7.5 > /etc/yum/vars/releasever` は RHEL バージョンを RHEL 7.5 に固定します。

1. RHEL VM を更新します。
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL VM を非 EUS に再び切り替える (バージョン ロックを削除)
次をルートとして実行します。
1. releasever ファイルを削除します。
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS リポジトリを無効にします。
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM を更新します。
    ```bash
    sudo yum update
    ```

## <a name="the-ips-for-the-rhui-content-delivery-servers"></a>RHUI コンテンツ配信サーバーの IP アドレス

RHUI は、RHEL のオンデマンド イメージが提供されているすべてのリージョンで利用できます。 現時点では、[Azure の状態ダッシュボード](https://azure.microsoft.com/status/) ページに掲載されているすべてのパブリック リージョン、Azure US Government リージョン、および Microsoft Azure Germany リージョンが含まれます。

ネットワーク構成を使用して RHEL PAYG VM からのアクセスをさらに制限している場合、現在の環境に応じて `yum update` が動作するよう次の IP が許可されていることを確認してください。


```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213
52.237.203.198

# Azure US Government
13.72.186.193
13.72.14.155
52.244.249.194

# Azure Germany
51.5.243.77
51.4.228.145
```

## <a name="azure-rhui-infrastructure"></a>Azure RHUI インフラストラクチャ


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>VM 上の有効期限が切れた RHUI クライアント証明書を更新する

RHEL 7.4 (イメージ URN: `RedHat:RHEL:7.4:7.4.2018010506`) などの古い RHEL VM イメージを使用している場合は、有効期限が切れた SSL クライアント証明書による RHUI への接続の問題が発生します。 _"SSL ピアは期限切れとして証明書を拒否しました"_ または _"エラー: リポジトリのリポジトリ メタデータ (repomd.xml) を取得できません: そのパスを確認し、もう一度お試しください"_ のようなエラーが表示される場合があります。 この問題を解決するには、次のコマンドを使用して VM 上の RHUI クライアント パッケージを更新してください。

```bash
sudo yum update -y --disablerepo='*' --enablerepo='*microsoft*'
```

別の方法として、`sudo yum update` を実行した場合も、他のリポジトリに関して "有効期限が切れた SSL 証明書" のエラーは表示されますが、(RHEL バージョンに応じて) クライアント証明書パッケージが更新されることがあります。 この更新が成功した場合、他の RHUI リポジトリへの正常な接続が復元されるため、`sudo yum update` を正常に実行できるようになります。

`yum update` の実行中に 404 エラーが発生した場合は、次を実行して yum キャッシュを更新してみてください。
```bash
sudo yum clean all;
sudo yum makecache
```

### <a name="troubleshoot-connection-problems-to-azure-rhui"></a>Azure RHUI への接続に関する問題のトラブルシューティング
Azure RHEL PAYG VM から Azure RHUI への接続で問題が発生した場合は、次の手順に従います。

1. Azure RHUI エンドポイントの VM 構成を確認します。

    1. `/etc/yum.repos.d/rh-cloud.repo` ファイルの `[rhui-microsoft-azure-rhel*]` セクションの `baseurl` に `rhui-[1-3].microsoft.com` への参照が含まれているかどうかを確認します。 含まれていれば、新しい Aure RHUI を使用していることになります。

    1. 次のパターン `mirrorlist.*cds[1-4].cloudapp.net` の場所をポイントしている場合、構成の更新が必要です。 古い VM スナップショットが使用されているため、新しい Azure RHUI をポイントするように更新する必要があります。

1. Azure でホストされている RHUI へのアクセスは、[[Azure データセンターの IP 範囲]](https://www.microsoft.com/download/details.aspx?id=41653) 内の VM に限定されます。

1. 新しい構成を使用し、VM が Azure IP 範囲から接続していることを確認したが、それでも Azure RHUI に接続できない場合は、Microsoft または Red Hat にサポート ケースを提出してください。

### <a name="infrastructure-update"></a>インフラストラクチャの更新

2016 年 9 月に、更新済みの Azure RHUI をデプロイしました。 2017 年 4 月に、以前の Azure RHUI をシャットダウンしました。 2016 年 9 月以降から RHEL PAYG イメージ (またはそれらのスナップショット) を使用している場合、新しい Azure RHUI への接続は自動的に行われています。 ただし、VM に以前のスナップショットがある場合、Azure RHUI にアクセスするには、以降のセクションの説明に従って構成を手動で更新する必要があります。

新しい Azure RHUI サーバーは、[Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) を使用してデプロイされます。 Traffic Manager では、リージョンに関係なく、どの VM からも 1 つのエンドポイント (rhui-1.microsoft.com) を使用できます。

### <a name="manual-update-procedure-to-use-the-azure-rhui-servers"></a>Azure RHUI サーバーを使用するための手動での更新手順
この手順は参照用にのみ提供されています。 RHEL PAYG イメージには既に、Azure RHUI に接続するための適切な構成があります。 Azure RHUI サーバーを使用するために構成を手動で更新するには、次の手順を実行します。

- RHEL 6 の場合:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel6.config' install 'rhui-azure-rhel6'
  ```

- RHEL 7 の場合:
  ```bash
  yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
  ```

## <a name="next-steps"></a>次の手順
* Azure Marketplace PAYG イメージから Red Hat Enterprise Linux VM を作成し、Azure でホストされる RHUI を使用する場合は、[Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/) にアクセスしてください。
* Azure での Red Hat イメージの詳細については、[ドキュメントのページ](./rhel-images.md)を参照してください。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。
