---
title: Red Hat Update Infrastructure (RHUI) | Microsoft Docs
description: "Microsoft Azure のオンデマンド Red Hat Enterprise Linux インスタンス用の Red Hat Update Infrastructure (RHUI) について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: BorisB2015
manager: timlt
editor: 
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/13/2017
ms.author: borisb
translationtype: Human Translation
ms.sourcegitcommit: 6b54633b6beed738a93070aa4235ee4e24333b5e
ms.openlocfilehash: e1867aa3c5339373b494744ba26c750bcc11b5b5
ms.lasthandoff: 02/16/2017


---
# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Azure のオンデマンド Red Hat Enterprise Linux VM 用 Red Hat Update Infrastructure (RHUI)
Azure Marketplace から利用できるオンデマンドの Red Hat Enterprise Linux (RHEL) イメージから作成した仮想マシンは、Azure にデプロイされた Red Hat Update Infrastructure (RHUI) にアクセスするよう登録されています。  オンデマンド RHEL インスタンスは、リージョンの yum リポジトリへのアクセス権を持ち、増分更新を受信することができます。

RHUI によって管理される yum リポジトリの一覧は、プロビジョニング中に RHEL インスタンスで構成されます。 特別な構成を行う必要はありません。RHEL インスタンスが最新の更新を取得できる状態になった後で `yum update` を実行してください。

> [!NOTE]
> Azure では、2016 年 9 月に Azure RHUI の更新版をデプロイし、2017 年 1 月に以前のバージョンの Azure RHUI を段階的シャットダウンを開始しています。 2016 年 9 月以降の RHEL イメージ (またはスナップショット) を使用しているお客様は、ほとんどの場合アクションは必要ありません。 一方、これ以前のスナップショットまたは VM を持っているお客様は、Azure RHUI へのアクセスが中断されないように構成を更新する必要があります。
> 

## <a name="rhui-azure-infrastructure-update"></a>RHUI Azure インフラストラクチャの更新
2016 年 9 月より、Azure では Red Hat Update Infrastructure (RHUI) サーバーの新しいセットが提供されています。 これらのサーバーは [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) と共にデプロイされるため、リージョンに関係なく単一のエンドポイント (rhui-1.micrsoft.com) を任意の VM で使用できます。 Azure Marketplace にある新しい RHEL 従量課金制 (PAYG) イメージ (日付が 2016 年 9 月以降のバージョン) では、新しい Azure RHUI サーバーがポイントされるため、追加アクションは不要です。

### <a name="determine-if-action-is-required"></a>アクションが必要かどうか判断する
Azure RHEL PAYG VM から Azure RHUI への接続で問題が生じている場合、次の手順に従います。
1. Azure RHUI エンドポイントの VM 構成を確認します。

    `/etc/yum.repos.d/rh-cloud.repo` ファイルの `[rhui-microsoft-azure-rhel*]` セクションでベース URL に `rhui-[1-3].microsoft.com` への参照が含まれているかを確認します。 含まれていれば、新しい Aure RHUI を使用していることになります。

    次のパターン `mirrorlist.*cds[1-4].cloudapp.net` の場所をポイントしている場合、構成の更新が必要です。

    新しい構成を使用しているのに Azure RHUI に接続できない場合は、Microsoft または Red Hat にサポート ケースを提出てください。

    > [!NOTE]
    > Azure でホストされている RHUI の利用は、 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。
    > 

2. この確認の際に以前の Azure RHUI がまだ利用可能で、自動的に構成を更新する場合は、次のコマンドを実行します。

    `sudo yum update RHEL6` または `sudo yum update RHEL7` (RHEL ファミリのバージョンによる)。

3. 以前のバージョンの Azure RHUI に接続できなくなった場合は、次のセクションに記述されている手動のステップに従います。

4. 影響を受けた VM のプロビジョニング元のソース イメージまたはスナップショットの構成を更新してください。

### <a name="phased-shutdown-of-the-old-azure-rhui"></a>以前のバージョンの Azure RHUI の段階的なシャットダウン
以前のバージョンの Azure RHUI のシャットダウン中、次の通りアクセスが制限されます。

1. 以前のバージョンの Azure RHUI へ既に接続している IP アドレス セットへのアクセス制限 (ACL) の強化。 起こりえる副作用: 以前のバージョンの Azure RHUI を使用し続ける場合、新しい VM はこの RHUI に接続できない場合があります。 動的 IP を持ち、シャットダウン/割り当て解除/起動シーケンスの対象となる RHEL VM は、新しい IP を受信する場合があります。このため、以前のバージョンの Azure RHUI への接続に失敗する場合があります。

2. ミラー コンテンツ配信サーバーのシャットダウン。 起こりえる副作用: シャットダウンされる CDS が増えるにつれて、以前のバージョンの Azure RHUI への接続が不可能になる時点までの間 `yum update` のサービス タイムが長くなり、タイムアウトが増える場合があります。

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>新しい RHUI コンテンツ配信サーバーの IP アドレス
ネットワーク構成を使用して RHEL PAYG VM からのアクセスをさらに制限している場合、現在の環境に応じて `yum update` が動作するよう次の IP が許可されていることを確認してください。 

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193

# Azure Germany
51.5.243.77
51.4.228.145
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>新しい Azure RHUI サーバーを使用するための手動更新の手順
(Curl を使用して) 公開キー署名をダウンロードします。

```bash
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

ダウンロードしたキーを検証します。

```bash
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

出力を確認し、`keyid` と `user ID packet` を検証します。

```bash
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

公開キーをインストールします。

```bash
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

クライアント RPM をダウンロードして検証し、インストールします。

RHEL 6 の場合は次のようにダウンロードします。

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

RHEL 7 の場合は次のようにします。

```bash
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

次のように検証します。

```bash
rpm -Kv azureclient.rpm
```

出力で、パッケージの署名が正しいことを確認します。

```bash
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

次のように RPM をインストールします。

```bash
sudo rpm -U azureclient.rpm
```

完了したら、VM から Azure RHUI にアクセスできることを確認します。

### <a name="all-in-one-script-for-automating-the-preceding-task"></a>前述のタスクを自動化するオールインワンのスクリプト
必要な場合は次のスクリプトを使用して、影響を受ける VM を新しい Azure RHUI サーバーに更新するタスクを自動化できます。

```sh
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>RHUI の概要
[Red Hat Update Infrastructure](https://access.redhat.com/products/red-hat-update-infrastructure) では、Red Hat 認定クラウド プロバイダーがホストしている Red Hat Enterprise Linux クラウド インスタンス用に yum リポジトリ コンテンツを管理するために、拡張性の高いソリューションを提供します。 アップストリームの Pulp プロジェクトに基づいて、RHUI では、クラウド プロバイダーが Red Hat でホストされるリポジトリ コンテンツをローカルでミラーし、独自のコンテンツを使ったカスタムのリポジトリを作成し、これらのリポジトリを負荷分散されたコンテンツ配信システムを介してエンド ユーザーの大規模グループが利用できるようにします。

## <a name="regions-where-rhui-is-available"></a>RHUI を使用できるリージョン
RHUI は、RHEL のオンデマンド イメージが提供されているすべてのリージョンで利用できます。 これには現在、[Azure の状態ダッシュボード](https://azure.microsoft.com/status/) ページに掲載されているすべてのパブリック リージョン、Azure 米国政府リージョン、Azure ドイツ リージョンが含まれます。 RHEL のオンデマンド イメージからプロビジョニングされた VM の料金には、RHUI の利用料が含まれています。 追加のリージョン/国ごとのクラウド製品リリースは、RHEL のオンデマンド提供が今後拡大されていくにつれて更新されます。

> [!NOTE]
> Azure でホストされている RHUI の利用は、 [Microsoft Azure データセンターの IP 範囲](https://www.microsoft.com/download/details.aspx?id=41653)内の VM に限定されます。
> 
> 

## <a name="get-updates-from-another-update-repository"></a>更新プログラムを他の更新リポジトリから取得する
更新プログラムを別の (Azure でホストされている RHUI 以外の) 更新リポジトリから取得する必要がある場合、まず RHUI からインスタンスの登録を解除する必要があります。 次に、目的の更新インフラストラクチャ (Red Hat Satellite、Red Hat カスタマー ポータル CDN など) に登録し直す必要があります。 そうしたサービスと [Azure の Red Hat Cloud Access](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure)の登録には、適切な Red Hat サブスクリプションが必要となります。

RHUI の登録を解除し、目的の更新インフラストラクチャに登録し直すには、次の手順に従います。

1. /etc/yum.repos.d/rh-cloud.repo を編集し、`enabled=1` をすべて `enabled=0` に変更します。 次に例を示します。
   
   ```bash
   sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo
   ```
   
2. /etc/yum/pluginconf.d/rhnplugin.conf を編集し、`enabled=0` を `enabled=1` に変更します。
3. 次に、目的のインフラストラクチャ (Red Hat カスタマー ポータルなど) に登録します。 [Red Hat カスタマー ポータルにシステムを登録してサブスクライブする方法](https://access.redhat.com/solutions/253273)については、Red Hat ソリューション ガイドに従ってください。

> [!NOTE]
> RHEL Pay-As-You-Go (PAYG: 従量課金) イメージの料金には、Azure でホストされる RHUI の利用料が含まれています。 Azure でホストされている RHUI から PAYG RHEL VM の登録を解除しても、仮想マシンが Bring-Your-Own-License (BYOL: ライセンス持ち込み ) タイプの VM に変換されることはありません。 そのため同じ VM を別の更新ソースに登録した場合、二重に料金が発生する可能性があります。1 つ目は Azure RHEL ソフトウェア料金で、2 つ目が Red Hat のサブスクリプションに対するものです。 
> 
> Azure でホストされている RHUI 以外の更新インフラストラクチャを常に使用する必要がある場合は、 [Azure 用の Red Hat ベースの仮想マシンを作成してアップロードする方法](virtual-machines-linux-redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) についての記事を参照して、独自の (BYOL タイプの) イメージを作成し、デプロイすることを検討してください。
> 

## <a name="next-steps"></a>次のステップ
Azure Marketplace の従量課金イメージから Red Hat Enterprise Linux VM を作成し、Azure でホストされる RHUI を利用するには、 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/)にアクセスしてください。 特別な設定を行わなくても、RHEL インスタンスで `yum update` を使用することができます。


