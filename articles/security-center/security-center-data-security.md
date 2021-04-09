---
title: Azure Security Center のデータ セキュリティ | Microsoft Docs
description: このドキュメントでは、Azure Security Center でデータがどのように管理および保護されているかについて説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596281"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center のデータ セキュリティ

Azure Security Center では、脅威の防止、検出、脅威への対応を支援するために、構成情報、メタデータ、イベント ログなど、セキュリティに関連するデータを収集および処理しています。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

この記事では、Security Center でデータがどのように管理および保護されているかについて説明します。

## <a name="data-sources"></a>データ ソース
Security Center を使用すると、次のソースからのデータを分析して、セキュリティ状態の可視化、脆弱性の識別、軽減策の提案、アクティブな脅威の検出を行うことができます。

- **Azure サービス**:デプロイされた Azure サービスの構成についての情報を、サービスのリソース プロバイダーと通信して使用します。
- **ネットワーク トラフィック**: 送信元/送信先 IP/ポート、パケット サイズ、ネットワーク プロトコルなど、Microsoft のインフラストラクチャからサンプリングされたネットワーク トラフィック メタデータを使用します。
- **パートナー ソリューション**: ファイアウォールやマルウェア対策ソリューションなど、統合されたパートナー ソリューションからのセキュリティの警告を使用します。
- **マシン**: ご利用のマシンからの構成の詳細、セキュリティ イベントに関する情報 (Windows のイベントおよび監査ログなど)、および syslog メッセージを使用します。


## <a name="data-protection"></a>データの保護

### <a name="data-segregation"></a>データの分離
データはサービス全体を通じて、各コンポーネントに論理的に分割されて保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。

### <a name="data-access"></a>データ アクセス
セキュリティに関する推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Azure サービスによって収集または分析された情報 (プロセス作成イベントおよびその他の成果物を含む) に Microsoft の担当者がアクセスする場合があります。これらの情報には、マシンにある顧客データや個人データが意図せずに含まれていることがあります。 

Microsoft は、[Microsoft Online Services のデータ保護追加契約](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)を遵守しています。これらの文書には、Microsoft が宣伝や他の商業上の目的のために顧客データを使用したり、そのデータから情報を取り出したりすることはないと記載されています。 ユーザーに Azure サービスを提供するため、およびそれに準じた目的のためにだけ、顧客データを使用します。 顧客データに対するすべての権限は、お客様が保持します。

### <a name="data-use"></a>データの使用
Microsoft は、防止と検出の機能を強化するために、複数のテナントにわたって見られるパターンおよび脅威インテリジェンスを使用します。その際に、[プライバシーに関する声明](https://privacy.microsoft.com/privacystatement)で説明されている誓約に従います。

## <a name="manage-data-collection-from-machines"></a>仮想マシンからデータ収集を管理する
Azure で Security Center を有効にすると、各 Azure サブスクリプションのデータ収集が有効になります。 Security Center で、サブスクリプションのデータ収集を有効にすることもできます。 データ収集が有効になっている場合、サポートされている既存の全 Azure 仮想マシンと新規に作成される仮想マシン上に Log Analytics エージェントが Security Center によってプロビジョニングされます。

Log Analytics エージェントは、さまざまなセキュリティ関連の構成をスキャンして、[Windows イベント トレーシング](/windows/win32/etw/event-tracing-portal) (ETW) トレースにイベント化します。 さらに、オペレーティング システムは、マシンの実行中にイベント ログ イベントを発生させます。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。 Log Analytics エージェントは、イベント ログ エントリと ETW トレースを読み取り、分析のためにそれらをワークスペースにコピーします。 また、Log Analytics エージェントによって、プロセス作成イベントとコマンド ラインの監査が有効化されます。

Azure Defender を使用していない場合は、セキュリティ ポリシーで仮想マシンからのデータ収集を無効にすることもできます。 Azure Defender によって保護されているサブスクリプションには、データ収集が必須です。 データ収集が無効になっていても、VM ディスクのスナップショットとアーティファクトの収集は引き続き有効になります。

ご利用のマシンから収集したデータが格納されるワークスペースとリージョンを指定できます。 既定では、マシンから収集されたデータは、次の表に示すように最も近いワークスペースに格納されます。

| VM Geo                                      | ワークスペース Geo  |
|---------------------------------------------|----------------|
| 米国、ブラジル、南アフリカ         | United States  |
| Canada                                      | Canada         |
| ヨーロッパ (英国を除く)           | ヨーロッパ         |
| イギリス                              | イギリス |
| アジア (インド、日本、韓国、中国を除く) | アジア太平洋   |
| 韓国                                       | アジア太平洋   |
| インド                                       | インド          |
| 日本                                       | 日本          |
| 中国                                       | 中国          |
| オーストラリア                                   | オーストラリア      |
|                                             |                |

> [!NOTE]
> **Azure Defender for Storage** を使用すると、成果物が、関連する Azure リソースの場所に応じてリージョンごとに格納されます。 詳細については、「[Azure Defender for Storage の概要](defender-for-storage-introduction.md)」を参照してください。


## <a name="data-consumption"></a>データの使用

顧客は、次のデータ ストリームから Security Center 関連のデータにアクセスできます。


| ストリーム                                                                                | データ型                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md)                       | セキュリティに関するすべてのアラート、承認された Security Center の [Just-In-Time](security-center-just-in-time.md) アクセス要求、[適応型アプリケーション制御](security-center-adaptive-application.md)によって生成されたすべてのアラート。|
| [Azure Monitor ログ](../azure-monitor/data-platform.md)                      | すべてのセキュリティ アラート。                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | セキュリティ アラート、セキュリティに関する推奨事項、脆弱性評価の結果、セキュリティで保護されたスコア情報、コンプライアンス チェックのステータスなど。                                                                       |
| [Azure Security Center REST API](/rest/api/securitycenter/) | セキュリティ アラート、セキュリティに関する推奨事項など。                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>次のステップ

この記事では、Azure Security Center でデータがどのように管理および保護されているかについて説明しました。 

Azure Security Center の詳細については、「[Azure Security Center とは](security-center-introduction.md)」を参照してください。