---
title: Azure Advisor によるアプリケーションの高可用性の向上 | Microsoft Docs
description: Azure Advisor を使用して、Azure のデプロイの高可用性を向上させます。
services: advisor
documentationcenter: NA
author: kasparks
ms.author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 42627649145b568b2b25411d182e5a36cdb025b0
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58881190"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Azure Advisor によるアプリケーションの高可用性の向上

Azure Advisor を使用して、ビジネスに不可欠なアプリケーションの継続稼働を保証し、さらに向上させることができます。 Advisor による高可用性に関する推奨事項は、Advisor ダッシュボードの **[高可用性]** タブで取得できます。

## <a name="ensure-virtual-machine-fault-tolerance"></a>仮想マシンのフォールト トレランスを確保する

アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、可用性セットの一部ではない仮想マシンを識別し、それらを可用性セットに移動することを推奨します。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンの可用性セットを作成するか、既存の可用性セットに仮想マシンを追加するかを選択できます。

> [!NOTE]
> 可用性セットを作成する場合は、少なくとも 1 台以上の仮想マシンを可用性セットに追加する必要があります。 2 台以上の仮想マシンを 1 つの可用性セットにグループ化して、障害の発生時に少なくとも 1 台のマシンを使用できるようにしておくことをお勧めします。

## <a name="ensure-availability-set-fault-tolerance"></a>可用性セットのフォールト トレランスを確保する

アプリケーションに冗長性をもたらすには、可用性セット内に 2 つ以上の仮想マシンをグループ化することをお勧めします。 Advisor は、1 台の仮想マシンのみを含む可用性セットを識別し、そのセットにさらに仮想マシンを追加することを推奨します。 このような構成により、計画済みのメンテナンス イベント中でも計画外のメンテナンス イベント中でも、少なくとも 1 台の仮想マシンが利用可能となり、Azure 仮想マシンの SLA が満たされます。 仮想マシンを作成するか、既存の仮想マシンを可用性セットに追加するかを選択できます。  

## <a name="use-managed-disks-to-improve-data-reliability"></a>データの信頼性を改善するために Managed Disks を使用する

ストレージ アカウントまたはストレージ スケール ユニットを共有するディスクが含まれる、可用性セット内の仮想マシンは、停止時の単一ストレージ スケール ユニット障害に対する回復性がありません。 Advisor では、これらの可用性セットを識別し、Azure Managed Disks への移行を推奨します。 そうすることで、単一障害点を避けるために、可用性セット内の異なる仮想マシンのディスクが十分に分離されるようにすることができます。 

## <a name="ensure-application-gateway-fault-tolerance"></a>アプリケーション ゲートウェイのフォールト トレランスを確保する

この推奨事項により、アプリケーション ゲートウェイを備えるミッション クリティカルなアプリケーションのビジネス継続性が確保されます。 Advisor は、フォールト トレランス用に構成されていないアプリケーション ゲートウェイ インスタンスを識別し、実施できる修復アクションを提案します。 Advisor は、中規模または大規模な単一インスタンス アプリケーション ゲートウェイを識別し、少なくとも 1 つ以上のインスタンスを追加することを推奨します。 また、1 つまたは複数の小規模アプリケーション ゲートウェイを識別し、中規模または大規模な SKU に移行することを推奨します。 これらの修復アクションでは、アプリケーション ゲートウェイ インスタンスがこれらのリソースの現在の SLA 要件を満たすように構成されていることを確認します。

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>誤って削除されないように、仮想マシンのデータを保護する

仮想マシンのバックアップを設定することにより、ビジネス クリティカルなデータの可用性が確保され、偶発的な削除または破損からデータを保護します。 Advisor は、バックアップが有効になっていない仮想マシンを識別子、バックアップを有効にすることを推奨します。 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>必要な時に Azure クラウドの専門家を利用できるようにする

ビジネスに不可欠なワークロードを実行しているとき、必要時にはテクニカル サポートを受けることができるようにしておくことが重要です。 Advisor は、サポート計画にテクニカル サポートが含まれないビジネスに不可欠なサブスクリプションを識別し、テクニカル サポートが含まれるオプションへのアップグレードを勧めします。

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure の問題による影響を受けたときに通知する Azure Service Health アラートを作成する

Azure サービスの問題による影響を受けたときに通知する Azure Service Health アラートを設定することをお勧めします。 [Azure Service Health](https://azure.microsoft.com/features/service-health/) は、Azure サービスの問題による影響を受けた場合に、カスタマイズされたガイダンスとサポートを提供する無料サービスです。 Advisor はアラートが構成されていないサブスクリプションを識別し、アラートの作成を推奨します。

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>回復性を考慮して Traffic Manager エンドポイントを構成する

複数のエンドポイントを含む Traffic Manager プロファイルは、いずれかのエンドポイントに障害が発生した場合でも高い可用性が確保されます。 エンドポイントを複数の異なるリージョンに配置すると、サービスの信頼性がさらに向上します。 Advisor はエンドポイントが 1 つだけの Traffic Manger プロファイルを識別し、別のリージョンに少なくとも 1 つのエンドポイントを追加することを推奨します。

近接ルーティング用に構成されている Traffic Manager プロファイル内のエンドポイントがすべて同じリージョン内にある場合、他のリージョンのユーザーに対しては接続の遅延が発生する可能性があります。 別のリージョンにエンドポイントを追加または移動すると、一方のリージョン内のすべてのエンドポイントに障害が発生した場合でも全体的なパフォーマンスと可用性が向上します。 Advisor は、近接ルーティング用に構成されていて、すべてのエンドポイントが同じリージョン内にある Traffic Manager プロファイルを識別します。 別の Azure リージョンへのエンドポイントの追加または移動を推奨します。

Traffic Manager プロファイルが地理的なルーティング用に構成されている場合は、定義済みのリージョンに基づいてトラフィックがエンドポイントにルーティングされます。 リージョンで障害が発生した場合、定義済みのフェールオーバーはありません。 エンドポイントで [リージョンのグループ化] を [すべて (世界)] に構成すると、トラフィックのドロップが回避され、サービスの可用性が向上します。 Advisor は、地理的なルーティング用に構成されていて、エンドポイントで [リージョンのグループ化] が [すべて (世界)] に構成されていない Traffic Manager プロファイルを識別します。 エンドポイントが [すべて (世界)] になるように構成を変更することを推奨します。

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Azure Storage アカウントでデータの論理的な削除を使用し、データを誤って上書きまたは削除した後にデータを保存して復旧する

ストレージ アカウントで[論理的な削除](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete)を有効にすると、削除された BLOB は完全には削除されずに、論理的に削除された状態に切り替わります。 データを上書きした場合、上書きされたデータの状態を保存するために、論理的に削除されたスナップショットが生成されます。 論理的な削除を使用すると、不注意で削除や上書きを行った場合に、回復できるようになります。 Advisor によって、論理的な削除が有効になっていない Azure Storage アカウントが特定され、有効にするように提案が行われます。

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>接続の回復性を高めるために VPN ゲートウェイをアクティブ/アクティブに構成する

アクティブ/アクティブ構成では、VPN ゲートウェイの両方のインスタンスが、オンプレミスの VPN デバイスへの S2S VPN トンネルを確立します。 計画的なメンテナンス イベントまたは計画外のイベントが 1 つのゲートウェイ インスタンスで発生すると、トラフィックはもう 1 つのアクティブな IPsec トンネルに自動的に切り替えられます。 Azure Advisor では、アクティブ/アクティブとして構成されていない VPN ゲートウェイが識別され、それらを高可用性のために構成することが推奨されます。

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor の高可用性に関する推奨事項にアクセスする方法

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

2.  Advisor ダッシュボードの **[高可用性]** タブをクリックします。

## <a name="next-steps"></a>次の手順

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)

