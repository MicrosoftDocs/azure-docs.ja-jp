---
title: Azure の Red Hat Enterprise Linux イメージ | Microsoft Docs
description: Microsoft Azure の Red Hat Enterprise Linux イメージについて説明します
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/18/2019
ms.author: borisb
ms.openlocfilehash: fb3c0e46324a22bdd95bf7d93c28e69c195927e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786036"
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
現在公開されているすべての RHEL イメージでは従量課金制モデルが使用され、[Azure の Red Hat Update Infrastructure (RHUI)](https://aka.ms/rhui-update) に接続されます。 RHUI の設計上の制限により、RHEL 7 ファミリのイメージには新しい名前付け規則が採用されています。 現時点では、RHEL 6 ファミリの名前付けは変更されていません。

制限により、非選択的な `yum update` が、RHUI に接続されている VM に対して実行されると、RHEL バージョンが、現在のファミリの最新のものに更新されることになります。 詳細については、[このリンク](https://aka.ms/rhui-update)をご覧ください。 これにより、プロビジョニングされた RHEL 7.2 イメージが更新後に RHEL 7.6 になったときに混乱が生じる場合があります。 上記の例に示すように、必要なバージョンを明示的に指定することで、より古いイメージから引き続きプロビジョニングすることができます。 新しい RHEL 7 イメージのプロビジョニング時に必要なバージョンが指定されていない場合は、最新のイメージがプロビジョニングされます。

>[!NOTE]
> RHEL for SAP イメージ セットでは、RHEL のバージョンは固定されたままです。 そのため、その名前付け規則には、SKU での特定のバージョンが含まれます。

>[!NOTE]
> RHEL 6 イメージ セットは、新しい名前付け規則には移動されませんでした。

現在、一般的に使用できるプランと SKU は以下のとおりです。

プラン| SKU | パーティション分割 | プロビジョニング | メモ
:----|:----|:-------------|:-------------|:-----
RHEL | 7-RAW | RAW | Linux エージェント | RHEL 7 イメージ ファミリ
| | 7-LVM | LVM | Linux エージェント | RHEL 7 イメージ ファミリ
| | 7-RAW-CI | RAW-CI | cloud-init | RHEL 7 イメージ ファミリ
| | 6.7 | RAW | Linux エージェント | RHEL 6.7 イメージ (古い名前付け規則)
| | 6.8 | RAW | Linux エージェント | RHEL 6.8 については上記と同じ
| | 6.9 | RAW | Linux エージェント | RHEL 6.9 については上記と同じ
| | 6.10 | RAW | Linux エージェント | RHEL 6.10 については上記と同じ
| | 7.2 | RAW | Linux エージェント | RHEL 7.2 については上記と同じ
| | 7.3 | RAW | Linux エージェント | RHEL 7.3 については上記と同じ
| | 7.4 | RAW | Linux エージェント | RHEL 7.4 については上記と同じ
| | 7.5 | RAW | Linux エージェント | RHEL 7.5 については上記と同じ
RHEL-SAP | 7.4 | LVM | Linux エージェント | RHEL 7.4 for SAP HANA および Business Apps
| | 7.5 | LVM | Linux エージェント | RHEL 7.5 for SAP HANA および Business Apps
RHEL-SAP-HANA | 6.7 | RAW | Linux エージェント | RHEL 6.7 for SAP HANA
| | 7.2 | LVM | Linux エージェント | RHEL 7.2 for SAP HANA
| | 7.3 | LVM | Linux エージェント | RHEL 7.3 for SAP HANA
RHEL-SAP-APPS | 6.8 | RAW | Linux エージェント | RHEL 6.8 for SAP Business Applications
| | 7.3 | LVM | Linux エージェント | RHEL 7.3 for SAP Business Applications

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
