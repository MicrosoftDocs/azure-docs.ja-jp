---
title: Azure の Red Hat Enterprise Linux イメージ | Microsoft Docs
description: Microsoft Azure の Red Hat Enterprise Linux イメージについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 6/6/2019
ms.author: borisb
ms.openlocfilehash: f7ae82b0376489e21b35e4e94dce32805bea69c6
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708377"
---
# <a name="red-hat-enterprise-linux-images-in-azure"></a>Azure の Red Hat Enterprise Linux イメージ
この記事では、名前付けと保有期間に関するポリシーと、Azure Marketplace 内で利用可能な Red Hat Enterprise Linux (RHEL) イメージについて説明します。

すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle \(Red Hat Enterprise Linux のライフ サイクル\)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。

>[!Important]
> Azure マーケットプレースで現在使用可能な RHEL イメージは、BYOS (Bring-Your-Own-Subscription)または従量課金制 (PAYG) ライセンス モデルをサポートしています。 [Azure ハイブリッド特典](../windows/hybrid-use-benefit-licensing.md)と、BYOS と PAYG の動的な切り替えはサポートされていません。 ライセンス モードの切り替えには、対応するイメージから VM を再デプロイする必要があります。

>[!Note]
> Azure マーケットプレース ギャラリーの RHEL イメージに関する問題については、Microsoft にサポート チケットを提出してください。

## <a name="images-available-in-the-ui"></a>UI で利用可能なイメージ
Marketplace で "Red Hat" を検索するときや、Azure portal の UI でリソースを作成するときに、利用可能な RHEL イメージと関連する Red Hat 製品のサブセットが表示されます。 Azure CLI/PowerShell/API を使用して、利用可能な VM イメージの完全なセットをいつでも取得することができます。

Azure で利用可能な Red Hat イメージの完全なセットを表示するには、次のコマンドを実行します

```azurecli-interactive
az vm image list --publisher RedHat --all
```

## <a name="naming-convention"></a>命名規則
Azure の VM イメージは、発行元、プラン、SKU、バージョンごとに整理されます。 発行元:プラン:SKU:バージョンの組み合わせはイメージ URN であり、使用されるイメージを一意に識別します。

たとえば、`RedHat:RHEL:7-RAW:7.6.2018103108` は、2018 年 10 月 31 日に構築された RHEL 7.6 の未加工のパーティション イメージを表します。

RHEL 7.6 VM を作成する方法の例を以下に示します。
```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:7.6.2018103108 --no-wait
```

### <a name="the-latest-moniker"></a>"最新" のモニカー
Azure REST API では、特定のバージョンではなく、バージョンで "最新" のモニカーを使用できます。 "最新" のものを使用することで、特定の発行元、プラン、SKU で利用可能な最新のイメージがプロビジョニングされます。

たとえば、`RedHat:RHEL:7-RAW:latest` は、利用可能な最新の RHEL 7 ファミリの未加工のパーティション イメージを表します。

```azurecli-interactive
az vm create --name RhelVM --resource-group TestRG --image RedHat:RHEL:7-RAW:latest --no-wait
```

>[!NOTE]
> 通常、最新のものを判断するためのバージョン比較では、[CompareTo メソッド](https://msdn.microsoft.com/library/a5ts8tb6.aspx)の規則に従います。

### <a name="current-naming-convention"></a>現在の名前付け規則
現在公開されているすべての RHEL イメージでは従量課金制モデルが使用され、[Azure の Red Hat Update Infrastructure (RHUI)](https://aka.ms/rhui-update) に接続されます。 新しい名前付け規則が RHEL 7 イメージ ファミリに採用されました。この規則では、バージョンの代わりに、ディスクのパーティション構成 (未加工、LVM) が SKU 内に指定されています。 RHEL イメージのバージョンには、7-RAW または 7-LVM のいずれかが含まれます。 現時点では、RHEL 6 ファミリの名前付けは変更されていません。

この名前付け規則には、次の 2 種類の RHEL 7 イメージ SKU があります。マイナー バージョンを一覧表示する SKU と、表示しない SKU です。 7-RAW または 7-LVM の SKU を使用する場合は、デプロイする RHEL マイナー バージョンをバージョン内に指定できます。 "最新" バージョンを選択した場合は、RHEL の最新のマイナー リリースがプロビジョニングされます。

>[!NOTE]
> RHEL for SAP イメージ セットでは、RHEL のバージョンは固定されたままです。 そのため、その名前付け規則には、SKU での特定のバージョンが含まれます。

>[!NOTE]
> RHEL 6 イメージ セットは、新しい名前付け規則には移動されませんでした。

## <a name="extended-update-support-eus"></a>Extended Update Support (EUS)
2019 年 4 月現在、RHEL イメージは、既定では Extended Update Support (EUS) リポジトリに接続されて提供されています。 RHEL EUS の詳細については、[Red Hat のドキュメント](https://access.redhat.com/articles/rhel-eus)をご覧ください。

VM を EUS に切り替える方法の手順と EUS サポート終了日の詳細については、[こちら](https://aka.ms/rhui-update#rhel-eus-and-version-locking-rhel-vms)をご覧ください。

>[!NOTE]
> EUS は、RHEL Extras ではサポートされていません。 つまり、通常 RHEL Extras チャネルから利用できるパッケージをインストールする場合、EUS を使用している間はそれを実行できないことになります。 Red Hat Extras の製品ライフサイクルの詳細については、[こちら](https://access.redhat.com/support/policy/updates/extras/)をご覧ください。

### <a name="for-customers-that-want-to-use-eus-images"></a>EUS イメージの使用を希望するお客様の場合:
EUS リポジトリに接続されているイメージを使用するお客様は、SKU に RHEL マイナー バージョン番号を含む RHEL イメージを使用する必要があります。 これらのイメージは、(LVM ではなく) 未加工パーティション分割されます。

たとえば、次の 2 つの RHEL 7.4 イメージが利用可能と表示されるとします。
```bash
RedHat:RHEL:7-RAW:7.4.2018010506
RedHat:RHEL:7.4:7.4.2019041718
```
この場合、`RedHat:RHEL:7.4:7.4.2019041718` は既定では EUS リポジトリに接続され、`RedHat:RHEL:7-RAW:7.4.2018010506` は既定では非 EUS リポジトリに接続されます。

### <a name="for-customers-that-dont-want-to-use-eus-images"></a>EUS イメージの使用を希望しないお客様の場合:
既定で EUS に接続されているイメージを使用しない場合は、SKU にマイナー バージョン番号を含まないイメージを使用してデプロイしてください。

#### <a name="rhel-images-with-eus"></a>EUS を含む RHEL イメージ
次の表は、SKU にマイナー バージョンを含む RHEL イメージに適用されます。

>[!NOTE]
> この記事の執筆時点で EUS をサポートしているのは、RHEL 7.4 以降のマイナー バージョンのみです。 EUS は、RHEL 7.3 以前ではサポートされなくなりました。

マイナー バージョン |EUS イメージの例              |EUS の状態                                                   |
:-------------|:------------------------------|:------------------------------------------------------------|
RHEL 7.4      |RedHat:RHEL:7.4:7.4.2019041718 | 2019 年 4 月以降に発行されたイメージは、既定で EUS になります|
RHEL 7.5      |RedHat:RHEL:7.5:7.5.2019060305 | 2019 年 6 月以降に発行されたイメージは、既定で EUS になります |
RHEL 7.6      |RedHat:RHEL:7.6:7.6.2019052206 | 2019 年 5 月以降に発行されたイメージは、既定で EUS になります  |
RHEL 8.0      |該当なし                            | EUS イメージは現在使用できません                 |


## <a name="list-of-rhel-images-available"></a>利用可能な RHEL イメージの一覧
現在、一般的に使用できるプランと SKU は以下のとおりです。

プラン| SKU | パーティション分割 | プロビジョニング | メモ
:----|:----|:-------------|:-------------|:-----
RHEL          | 7-RAW    | RAW    | Linux エージェント | RHEL 7 イメージ ファミリ。 <br> 既定では EUS リポジトリに接続されていません。
|             | 7-LVM    | LVM    | Linux エージェント | RHEL 7 イメージ ファミリ。 <br> 既定では EUS リポジトリに接続されていません。
|             | 7-RAW-CI | RAW-CI | cloud-init  | RHEL 7 イメージ ファミリ。 <br> 既定では EUS リポジトリに接続されていません。
|             | 6.7      | RAW    | Linux エージェント | RHEL 6.7 イメージ (古い名前付け規則)
|             | 6.8      | RAW    | Linux エージェント | RHEL 6.8 については上記と同じ
|             | 6.9      | RAW    | Linux エージェント | RHEL 6.9 については上記と同じ
|             | 6.10     | RAW    | Linux エージェント | RHEL 6.10 については上記と同じ
|             | 7.2      | RAW    | Linux エージェント | RHEL 7.2 については上記と同じ
|             | 7.3      | RAW    | Linux エージェント | RHEL 7.3 については上記と同じ
|             | 7.4      | RAW    | Linux エージェント | RHEL 7.4 については上記と同じ。 <br> 2019 年 4 月現在、既定で EUS リポジトリに接続されています
|             | 7.5      | RAW    | Linux エージェント | RHEL 7.5 については上記と同じ。 <br> 2019 年 6 月現在、既定で EUS リポジトリに接続されています
|             | 7.6      | RAW    | Linux エージェント | RHEL 7.6 については上記と同じ。 <br> 2019 年 5 月現在、既定で EUS リポジトリに接続されています
RHEL-SAP      | 7.4      | LVM    | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps
|             | 7.5      | LVM    | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps
RHEL-SAP-HANA | 6.7      | RAW    | Linux エージェント | RHEL 6.7 for SAP HANA
|             | 7.2      | LVM    | Linux エージェント | RHEL 7.2 for SAP HANA
|             | 7.3      | LVM    | Linux エージェント | RHEL 7.3 for SAP HANA
RHEL-SAP-APPS | 6.8      | RAW    | Linux エージェント | RHEL 6.8 for SAP Business Applications
|             | 7.3      | LVM    | Linux エージェント | RHEL 7.3 for SAP Business Applications

### <a name="old-naming-convention"></a>以前の名前付け規則
RHEL 7 イメージ ファミリと RHEL 6 イメージ ファミリでは、前述の名前付け規則の変更が行われるまで、SKU の特定のバージョンが使用されていました。

完全なイメージ リストには、SKU が数値で示されます。 新しいマイナー リリースの発表時に、Microsoft および Red Hat によって新しい数値の SKU が作成されます。

### <a name="other-available-offers-and-skus"></a>その他の利用可能なプランと SKU
利用可能なプランと SKU の完全なリストには、上記の表にリストされているもの以外の追加のイメージ (`RedHat:rhel-ocp-marketplace:rhel74:7.4.1` など) が含まれる場合があります。 これらのプランは特定のマーケットプレース ソリューションのサポートを提供するために使用される場合があります。あるいは、プレビューとテストの目的で発行される可能性があります。 これらは、警告なく随時変更または削除される可能性があります。 Microsoft または Red Hat によって公式に文書化されていない限り、これらを使用しないでください。

## <a name="publishing-policy"></a>発行ポリシー
Microsoft および Red Hat では、新しいマイナー バージョンのリリース時、特定の CVE に対応する必要がある場合、あるいは不定期の構成変更/更新のために、イメージを更新します。 できるだけ早く (CVE の修正プログラムがリリースされてから、または利用可能になってから 3 営業日内に) 更新されたイメージを提供できるよう努めています。

特定のイメージ ファミリでのみ、現在のマイナー リリースを更新します。 新しいマイナー バージョンをリリースする場合、以前のマイナー バージョンの更新を停止します。 たとえば、RHEL 7.6 をリリースする場合、RHEL 7.5 イメージは更新されなくなります。

>[!NOTE]
> RHEL 従量課金制イメージからプロビジョニングされたアクティブな Azure VM は、Azure RHUI に接続されます。これらの VM では、Red Hat によってリリースされ、Azure RHUI にレプリケートされるとすぐに (通常は、Red Hat による公式リリース後 24 時間未満で) 更新プログラムと修正プログラムを受け取ることができます。 これらの VM には、更新プログラムを取得するために新たに発行されるイメージは必要ありません。更新を開始するタイミングはお客様が完全に制御できます。

## <a name="image-retention-policy"></a>イメージの保持期間ポリシー
現在のポリシーは、以前に発行されたすべてのイメージを保持するというものです。 Microsoft には、何らかの問題を発生させる原因になるイメージを削除する権利があります。 たとえば、今後のプラットフォームまたはコンポーネントの更新により構成が不適切となるイメージは削除される可能性があります。 削除される可能性のあるイメージでは、現在の Marketplace ポリシーに従い、イメージが削除される最大 30 日前に通知を提供します。

## <a name="next-steps"></a>次の手順
* Azure Red Hat Update Infrastructure の詳細については、[こちら](https://aka.ms/rhui-update)を参照してください。
* すべてのバージョンの RHEL に対する Red Hat のサポート ポリシーに関する情報は、「[Red Hat Enterprise Linux Life Cycle (Red Hat Enterprise Linux のライフ サイクル)](https://access.redhat.com/support/policy/updates/errata)」ページに記載されています。
