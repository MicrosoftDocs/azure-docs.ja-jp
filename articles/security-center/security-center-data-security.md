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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 987cdd76ba533fa0ae4b37c2755fe84a00d14de5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/31/2020
ms.locfileid: "80435862"
---
# <a name="azure-security-center-data-security"></a>Azure Security Center のデータ セキュリティ
Azure Security Center では、脅威に対する防御と検出、対応を支援するために、構成情報、メタデータ、イベント ログ、クラッシュ ダンプ ファイルなど、セキュリティに関連するさまざまなデータを収集、処理しています。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

この記事では、Azure Security Center でデータがどのように管理および保護されているかについて説明します。

## <a name="data-sources"></a>データ ソース
Azure Security Center は、セキュリティ状態の可視化、脆弱性の識別、軽減策の提案、アクティブな脅威の検出のために、次のソースからのデータを分析します。

- Azure サービス:デプロイされた Azure サービスの構成についての情報を、サービスのリソース プロバイダーと通信して使用します。
- ネットワーク トラフィック:送信元/送信先 IP/ポート、パケット サイズ、ネットワーク プロトコルなど、Microsoft のインフラストラクチャからサンプリングされたネットワーク トラフィック メタデータを使用します。
- パートナー ソリューション:ファイアウォールやマルウェア対策ソリューションなど、統合されたパートナー ソリューションからのセキュリティの警告を使用します。
- 仮想マシンとサーバー:仮想マシンからの構成情報とセキュリティ イベントについての情報 (Windows のイベントおよび監査ログ、IIS ログ、syslog メッセージ、クラッシュ ダンプ ファイルなど) を使用します。 さらに、警告が作成されると、Azure Security Center は、科学捜査の目的で、影響を受けた VM ディスクのスナップショットを生成し、VM ディスクからの警告に関連するマシン アーティファクト (レジストリ ファイルなど) を抽出する場合があります。


## <a name="data-protection"></a>データ保護
**データの分離**:データはサービス全体を通じて、各コンポーネントに論理的に分割されて保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。

**データ アクセス**:セキュリティに関する推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Azure サービスによって収集または分析された情報 (クラッシュ ダンプ ファイル、プロセス作成イベント、VM ディスク スナップショット、アーティファクトなど) に Microsoft の担当者がアクセスする場合があります。これらの情報には、仮想マシンにある顧客データまたは個人データが意図せずに含まれる場合があります。 Microsoft は、[Microsoft オンライン サービスの使用条件とプライバシーに関する声明](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)を遵守しています。これらの文書には、Microsoft が宣伝や他の商業上の目的のために顧客データを使用したり、そのデータから情報を取り出したりすることはないと記載されています。 ユーザーに Azure サービスを提供するため、およびそれに準じた目的のためにだけ、顧客データを使用します。 顧客データに対するすべての権限は、お客様が保持します。

**データの使用**:Microsoft は、防止と検出の機能を強化するために、複数のテナントにわたって見られるパターンおよび脅威インテリジェンスを使用します。その際に、[プライバシーに関する声明](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx)で説明されている誓約に従います。

## <a name="data-location"></a>データの場所

**ワークスペース**:ワークスペースは次の geo に指定され、Azure 仮想マシンから収集されたデータ (クラッシュ ダンプや一部の種類のアラートのデータを含む) は最も近いワークスペースに格納されます。

| VM Geo                              | ワークスペース Geo |
|-------------------------------------|---------------|
| 米国、ブラジル、南アフリカ | 米国 |
| カナダ                              | カナダ        |
| ヨーロッパ (英国を除く)   | ヨーロッパ        |
| イギリス                      | イギリス |
| アジア (インド、日本、韓国、中国を除く)   | アジア太平洋  |
| 韓国                              | アジア太平洋  |
| インド                               | インド         |
| 日本                               | 日本         |
| 中国                               | 中国         |
| オーストラリア                           | オーストラリア     |


VM ディスクのスナップショットは、VM ディスクと同じストレージ アカウントに格納されます。

オンプレミスなど他の環境で実行している仮想マシンとサーバーの場合は、収集したデータを格納するワークスペースとリージョンを指定できます。

**Azure Security Center ストレージ**:パートナーの警告を含めたセキュリティの警告に関する情報は、関連する Azure リソースの場所に応じたリージョンに格納されます。一方、セキュリティの正常性状態と推奨事項に関する情報については、お客様の場所に応じて米国またはヨーロッパのいずれかに一元的に格納されます。
Azure Security Center は、クラッシュ ダンプ ファイルの一時的なコピーを収集し、悪用の試行と成功した侵害の証拠がないか分析します。 Azure Security Center は、この分析をワークスペースと同じ geo 内で行い、分析が完了すると、一時的なコピーを削除します。

マシン アーティファクトは、VM と同じリージョンに一元的に格納されます。


## <a name="managing-data-collection-from-virtual-machines"></a>仮想マシンからのデータ収集の管理

Azure で Security Center を有効にすると、各 Azure サブスクリプションのデータ収集が有効になります。 Azure Security Center の [セキュリティ ポリシー] セクションで、サブスクリプションのデータ収集を有効にすることもできます。 データ収集が有効になっている場合、Azure Security Center は、サポートされている既存の全 Azure 仮想マシンと新規に作成される仮想マシンに、Log Analytics エージェントをプロビジョニングします。
Log Analytics エージェントは、さまざまなセキュリティ関連の構成をスキャンして、[Windows イベント トレーシング](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) トレースにイベント化します。 さらに、オペレーティング システムは、マシンの実行中にイベント ログ イベントを発生させます。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。 Log Analytics エージェントは、イベント ログ エントリと ETW トレースを読み取り、分析のためにそれらをワークスペースにコピーします。 また Log Analytics エージェントによって、ワークスペースにクラッシュ ダンプ ファイルがコピーされるほか、プロセス作成イベントとコマンド ラインの監査が有効となります。

Azure Security Center Free を使用している場合は、セキュリティ ポリシーで仮想マシンからのデータ収集を無効にすることができます。 データ収集は、Standard レベルのサブスクリプションでは必須の機能です。 データ収集が無効になっていても、VM ディスクのスナップショットとアーティファクトの収集は引き続き有効になります。

## <a name="data-consumption"></a>データの使用

Security Center に関連したデータは、以下に示した各種のデータ ストリームから使用できます。

* **Azure アクティビティ**: セキュリティに関するあらゆるアラート、承認された Security Center の [Just-In-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) 要求、[適応型アプリケーション制御](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)によって生成されたあらゆるアラート。
* **Azure Monitor ログ**: セキュリティに関するあらゆるアラート。


> [!NOTE]
> セキュリティの推奨事項は、REST API 経由で使用することもできます。 詳細については、「[Security Resource Provider REST API Reference (セキュリティ リソース プロバイダーの REST API リファレンス)](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx)」を参照してください。

## <a name="see-also"></a>関連項目
この記事では、Azure Security Center でデータがどのように管理および保護されているかについて説明しました。 Azure Security Center の詳細については、以下の記事を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md) 」 -- Azure Security Center を導入するための設計上の考慮事項を計画および理解する方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure セキュリティ ブログ](https://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
