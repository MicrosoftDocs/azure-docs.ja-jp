---
title: Red Hat Update Infrastructure | Microsoft Docs
description: Microsoft Azure のオンデマンド Red Hat Enterprise Linux インスタンス用の Red Hat Update Infrastructure について説明します
author: asinn826
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 02/10/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 968377ed09996b9a717e0739a3de8355d1c8d88d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677135"
---
# <a name="red-hat-update-infrastructure-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure
 クラウド プロバイダー (Azure など) は、[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) (RHUI) を使用して、Red Hat でホストされているリポジトリのコンテンツのミラーリング、Azure 固有のコンテンツを使用したカスタム リポジトリの作成、およびエンド ユーザーの VM での使用を実行できます。

Red Hat Enterprise Linux (RHEL) 従量課金制 (PAYG) イメージには、Azure RHUI にアクセスするための構成が事前に設定されています。 追加の構成は必要ありません。 最新の更新プログラムを取得するには、RHEL インスタンスの準備ができてから `sudo yum update` を実行します。 このサービスは、RHEL PAYG ソフトウェア料金の一部として含まれています。

Azure での RHEL イメージに関する追加情報 (公開および保持ポリシーを含む) は[ここ](./redhat-images.md)で入手できます。

すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。

> [!IMPORTANT]
> RHUI は、従量課金制 (PAYG) イメージ のみを対象としています。 カスタム イメージおよびゴールド イメージ (別名 Bring-Your-Own-Subscription (BYOS)) の場合、更新プログラムを受信するには、システムを RHSM またはサテライトに接続する必要があります。 詳細については、[Red Hat の記事](https://access.redhat.com/solutions/253273) を参照してください。


## <a name="important-information-about-azure-rhui"></a>Azure RHUI に関する重要な情報

* Azure RHUI は、Azure で作成されるすべての RHEL PAYG VM をサポートする更新インフラストラクチャです。 これは、お使いの PAYG RHEL VM を Subscription Manager や Satellite、またはその他の更新ソースに登録することを妨げるものではありませんが、PAYG VM でそれを行うと、間接的に二重請求が発生します。 詳しくは、以下の点を参照してください。
* Azure でホストされている RHUI へのアクセスは、RHEL PAYG イメージの料金に含まれています。 Azure でホストされている RHUI から PAYG RHEL VM の登録を解除した場合は、仮想マシンが Bring-Your-Own-License (BYOL: ライセンス持ち込み) タイプの VM に変換されません。 そのため、別の更新ソースに同じ VM を登録した場合は、_間接_ 料金が二重に発生する可能性があります。 1 つ目は Azure RHEL ソフトウェア料金に対するものです。 2 つ目は、以前に購入した Red Hat のサブスクリプションに対するものです。 Azure でホストされている RHUI 以外の更新インフラストラクチャを常に使用する必要がある場合は、[RHEL BYOS イメージ](./byos.md)を使用するための登録を検討してください。

* Azure での RHEL SAP PAYG イメージ (RHEL for SAP、RHEL for SAP HANA、および RHEL for SAP Business Applications) は、SAP 認定に必要な特定の RHEL マイナー バージョンのままになっている専用の RHUI チャネルに接続されます。

* Azure でホストされている RHUI へのアクセスは、[データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。 すべての VM トラフィックをオンプレミスのネットワーク インフラストラクチャ経由でプロキシ処理している場合は、RHEL PAYG VM 用のユーザー定義のルートを設定して Azure RHUI にアクセスしなければならない場合があります。 その場合は、_すべての_ RHUI IP アドレスについてユーザー定義のルートを追加する必要があります。


## <a name="image-update-behavior"></a>イメージ更新の動作

2019 年 4 月の時点で、Azure には、既定で Extended Update Support (EUS) リポジトリに接続されている RHEL イメージと、既定で通常の (EUS 以外の) リポジトリに接続されている RHEL イメージが用意されています。 RHEL EUS に関する詳細情報は、Red Hat の[バージョン ライフサイクルのドキュメント](https://access.redhat.com/support/policy/updates/errata)および [EUS のドキュメント](https://access.redhat.com/articles/rhel-eus)で入手できます。 リポジトリごとに別のイメージが接続されているため、`sudo yum update` の既定の動作は、どちらの RHEL イメージからプロビジョニングしたかによって異なります。

完全なイメージの一覧を取得するには、Azure CLI を使用して `az vm image list --publisher redhat --all` を実行します。

### <a name="images-connected-to-non-eus-repositories"></a>EUS 以外のリポジトリに接続されているイメージ

EUS 以外のリポジトリに接続されている RHEL イメージから VM をプロビジョニングした場合は、`sudo yum update` を実行すると、最新の RHEL マイナー バージョンにアップグレードされます。 たとえば、RHEL 7.4 PAYG イメージから VM をプロビジョニングして `sudo yum update` を実行した場合は、RHEL 7.8 VM (RHEL7 ファミリ内の最新のマイナー バージョン) にアップグレードされます。

EUS 以外のリポジトリに接続されているイメージでは、SKU にマイナー バージョン番号は含まれません。 この SKU は、URN (イメージのフルネーム) 内の 3 番目の要素です。 たとえば、次のイメージはすべて EUS 以外のリポジトリに接続されています。

```text
RedHat:RHEL:7-LVM:7.4.2018010506
RedHat:RHEL:7-LVM:7.5.2018081518
RedHat:RHEL:7-LVM:7.6.2019062414
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7-RAW:7.5.2018081518
RedHat:RHEL:7-RAW:7.6.2019062120
```

SKU が 7-LVM と 7-RAW のどちらかであることに注意してください。 マイナー バージョンは、これらのイメージのバージョン (URN 内の 4 番目の要素) で示されます。

### <a name="images-connected-to-eus-repositories"></a>EUS リポジトリに接続されているイメージ

EUS リポジトリに接続されている RHEL イメージから VM をプロビジョニングした場合は、`sudo yum update` を実行しても、最新の RHEL マイナー バージョンにはアップグレードされません。 これは、EUS リポジトリに接続されているイメージも、その特定のマイナー バージョンにバージョン ロックされるためです。

EUS リポジトリに接続されているイメージでは、SKU にマイナー バージョン番号が含まれます。 たとえば、次のイメージはすべて EUS リポジトリに接続されています。

```text
RedHat:RHEL:7.4:7.4.2019062107
RedHat:RHEL:7.5:7.5.2019062018
RedHat:RHEL:7.6:7.6.2019062116
```

## <a name="rhel-eus-and-version-locking-rhel-vms"></a>RHEL EUS およびバージョン固定の RHEL VM

Extended Update Support (EUS) リポジトリは、VM をプロビジョニングした後に RHEL VM を特定の RHEL マイナー リリースに固定したいお客様が利用できます。 リポジトリを Extended Update Support リポジトリを指すように更新することによって、RHEL VM を特定のマイナー バージョンに固定できます。 また、EUS バージョン ロック操作を取り消すこともできます。

>[!NOTE]
> EUS は、RHEL Extras ではサポートされていません。 つまり、通常 RHEL Extras チャネルから利用できるパッケージをインストールする場合、EUS を使用している間はそれを実行できないことになります。 Red Hat Extras の製品ライフサイクルの詳細については、[こちら](https://access.redhat.com/support/policy/updates/extras/)をご覧ください。

本書の執筆時点では、RHEL <= 7.4 の EUS サポートは終了しています。 詳細については、[Red Hat ドキュメント](https://access.redhat.com/support/policy/updates/errata/#Long_Support)の「Red Hat Enterprise Linux の延長メンテナンス」セクションを参照してください。
* RHEL 7.4 EUS サポートは、2019 年 8 月 31 日に終了します
* RHEL 7.5 EUS サポートは、2020 年 4 月 30 日に終了します
* RHEL 7.6 EUS サポートは、2021 年 5 月 31 日に終了します
* RHEL 7.7 EUS サポートは、2021 年 8 月 30 日に終了します

### <a name="switch-a-rhel-vm-7x-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM 7.x を EUS に切り替える (特定のマイナー バージョンにバージョン ロックする)
RHEL 7.x VM を特定のマイナー リリースに固定するには、次の手順を使用します (ルートとして実行)。

>[!NOTE]
> このことは、EUS が利用できるバージョンの RHEL 7.x にのみ当てはまります。 この記事の作成時点で、これには RHEL 7.2-7.7 が含まれます。 詳しくは、[Red Hat Enterprise Linux のライフ サイクル](https://access.redhat.com/support/policy/updates/errata)に関するページをご覧ください。

1. EUS 以外のリポジトリを無効にします。
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7'
    ```

1. EUS リポジトリを追加します。
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7-eus.config' install 'rhui-azure-rhel7-eus'
    ```

1. `releasever` 変数をロックします (ルートとして実行):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上の命令は、RHEL マイナー リリースを現在のマイナー リリースに固定します。 アップグレードに固定しており、最新ではない将来のマイナー リリースに固定する場合は、特定のマイナー リリースを入力します。 たとえば、`echo 7.5 > /etc/yum/vars/releasever` は RHEL バージョンを RHEL 7.5 に固定します。

1. RHEL VM を更新します。
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-vm-8x-to-eus-version-lock-to-a-specific-minor-version"></a>RHEL VM 8.x を EUS に切り替える (特定のマイナー バージョンにバージョン ロックする)
RHEL 8.x VM を特定のマイナー リリースに固定するには、次の手順を使用します (ルートとして実行)。

>[!NOTE]
> このことは、EUS が利用できるバージョンの RHEL 8.x にのみ当てはまります。 この記事の作成時点で、これには RHEL 8.1-8.2 が含まれます。 詳しくは、[Red Hat Enterprise Linux のライフ サイクル](https://access.redhat.com/support/policy/updates/errata)に関するページをご覧ください。

1. EUS 以外のリポジトリを無効にします。
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8'
    ```

1. EUS リポジトリ構成ファイルを取得します。
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8-eus.config
    ```

1. EUS リポジトリを追加します。
    ```bash
    yum --config=rhui-microsoft-azure-rhel8-eus.config install rhui-azure-rhel8-eus
    ```

1. `releasever` 変数をロックします (ルートとして実行):
    ```bash
    echo $(. /etc/os-release && echo $VERSION_ID) > /etc/yum/vars/releasever
    ```

    >[!NOTE]
    > 上の命令は、RHEL マイナー リリースを現在のマイナー リリースに固定します。 アップグレードに固定しており、最新ではない将来のマイナー リリースに固定する場合は、特定のマイナー リリースを入力します。 たとえば、`echo 8.1 > /etc/yum/vars/releasever` は RHEL バージョンを RHEL 8.1 に固定します。

    >[!NOTE]
    > releasever にアクセスするためのアクセス許可の問題がある場合は、'nano/etc/yum/vars/releaseve' を使用してファイルを編集し、イメージ バージョンの詳細を追加して保存できます ('Ctrl + o'、Enter キー、'Ctrl + x' キーの順に押します)。  

1. RHEL VM を更新します。
    ```bash
    sudo yum update
    ```


### <a name="switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL 7.x VM を非 EUS に再び切り替える (バージョン ロックを削除)
次をルートとして実行します。
1. `releasever` ファイルを削除します。
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS リポジトリを無効にします。
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel7-eus'
   ```

1. RHEL VM の構成
    ```bash
    yum --config='https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel7.config' install 'rhui-azure-rhel7'
    ```

1. RHEL VM を更新します。
    ```bash
    sudo yum update
    ```

### <a name="switch-a-rhel-8x-vm-back-to-non-eus-remove-a-version-lock"></a>RHEL 8.x VM を非 EUS に再び切り替える (バージョン ロックを削除)
次をルートとして実行します。
1. `releasever` ファイルを削除します。
    ```bash
    rm /etc/yum/vars/releasever
     ```

1. EUS リポジトリを無効にします。
    ```bash
    yum --disablerepo='*' remove 'rhui-azure-rhel8-eus'
   ```

1. 標準リポジトリ構成ファイルを取得します。
    ```bash
    wget https://rhelimage.blob.core.windows.net/repositories/rhui-microsoft-azure-rhel8.config
    ```

1. EUS リポジトリを追加します。
    ```bash
    yum --config=rhui-microsoft-azure-rhel8.config install rhui-azure-rhel8
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
>[!NOTE]
>新しい Azure US Government のイメージでは、2020 年 1 月現在、上記の「Azure Global」ヘッダーの下に記載されているパブリック IP を使用します。

>[!NOTE]
>また、Azure Germany はパブリックなドイツ リージョンを優先した結果廃止されたことにも注意してください。 Azure Germany のお客様向けの推奨事項は、[こちら](#manual-update-procedure-to-use-the-azure-rhui-servers)の手順を使用してパブリック RHUI をポイントすることです。

## <a name="azure-rhui-infrastructure"></a>Azure RHUI インフラストラクチャ


### <a name="update-expired-rhui-client-certificate-on-a-vm"></a>VM 上の有効期限が切れた RHUI クライアント証明書を更新する

RHEL 7.4 (イメージ URN: `RedHat:RHEL:7.4:7.4.2018010506`) などの古い RHEL VM イメージを使用している場合は、有効期限が切れた TLS/SSL クライアント証明書に起因する RHUI への接続の問題が発生します。 _"SSL ピアは期限切れとして証明書を拒否しました"_ または _"エラー: リポジトリのリポジトリ メタデータ (repomd.xml) を取得できません: そのパスを確認し、もう一度お試しください"_ のようなエラーが表示される場合があります。 この問題を解決するには、次のコマンドを使用して VM 上の RHUI クライアント パッケージを更新してください。

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

- RHEL 8 の場合:
    1. config ファイルを作成します。
        ```bash
        vi rhel8.config
        ```
    1. 次の内容を構成ファイルに追加します。
        ```bash
        [rhui-microsoft-azure-rhel8]
        name=Microsoft Azure RPMs for Red Hat Enterprise Linux 8
        baseurl=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-2.microsoft.com/pulp/repos/microsoft-azure-rhel8 https://rhui-3.microsoft.com/pulp/repos/microsoft-azure-rhel8
        enabled=1
        gpgcheck=1
        gpgkey=https://rhelimage.blob.core.windows.net/repositories/RPM-GPG-KEY-microsoft-azure-release sslverify=1
        ```
    1. ファイルを保存し、次のコマンドを実行します。
        ```bash
        dnf --config rhel8.config install 'rhui-azure-rhel8'
        ```
    1. VM を更新します
        ```bash
        sudo dnf update
        ```


## <a name="next-steps"></a>次のステップ
* Azure Marketplace PAYG イメージから Red Hat Enterprise Linux VM を作成し、Azure でホストされる RHUI を使用する場合は、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RHEL_6) にアクセスしてください。
* Azure での Red Hat イメージの詳細については、[ドキュメントのページ](./redhat-images.md)を参照してください。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。
