---
title: "Azure Security Center のデータ セキュリティ | Microsoft Docs"
description: "このドキュメントでは、Azure Security Center でデータがどのように管理および保護されているかについて説明します。"
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/06/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 9852981e530cd147c2d34ac2ede251b58a167a0a
ms.openlocfilehash: 5c030f463b21284c15752cf95aa1f9a75f17ffb0
ms.lasthandoff: 02/07/2017


---
# <a name="azure-security-center-data-security"></a>Azure Security Center のデータ セキュリティ
Azure Security Center では、脅威に対する防御と検出、対応を支援するために、構成情報、メタデータ、イベント ログ、クラッシュ ダンプ ファイルなど、セキュリティに関連するさまざまなデータを収集、処理しています。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。

この記事では、Azure Security Center でデータがどのように管理および保護されているかについて説明します。


## <a name="data-sources"></a>データ ソース
Azure Security Center は、セキュリティ状態の可視化、脆弱性の識別、軽減策の提案、アクティブな脅威の検出のために、次のソースからのデータを分析します。

- Azure サービス: デプロイされた Azure サービスの構成についての情報を、サービスのリソース プロバイダーと通信して使用します。
- ネットワーク トラフィック: 送信元/送信先 IP/ポート、パケット サイズ、ネットワーク プロトコルなど、Microsoft のインフラストラクチャからサンプリングされたネットワーク トラフィック メタデータを使用します。
- パートナー ソリューション: ファイアウォールやマルウェア対策ソリューションなど、統合されたパートナー ソリューションからのセキュリティの警告を使用します。
- 仮想マシン: 仮想マシンからの構成情報とセキュリティ イベントについての情報 (Windows のイベントおよび監査ログ、IIS ログ、syslog メッセージ、クラッシュ ダンプ ファイルなど) を使用します。


## <a name="data-protection"></a>データ保護
**データの分離**: データはサービス全体を通じて、各コンポーネントに論理的に分割されて保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。

**データ アクセス**: セキュリティに関する推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Azure サービスによって収集または分析された情報 (クラッシュ ダンプ ファイル、プロセス作成イベント、VM ディスク スナップショット、アーティファクトなど) に Microsoft の担当者がアクセスする場合があります。これらの情報には、仮想マシンにある顧客データまたは個人データが意図せずに含まれる場合があります。 Microsoft は、[Microsoft オンライン サービスの使用条件とプライバシーに関する声明](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)を遵守しています。これらの文書には、Microsoft が宣伝や他の商業上の目的のために顧客データを使用したり、そのデータから情報を取り出したりすることはないと記載されています。 ユーザーに Azure サービスを提供するため、およびそれに準じた目的のためにだけ、顧客データを使用します。 顧客データに対するすべての権限は、お客様が保持します。

**データの使用**: Microsoft は、防止と検出の機能を強化するために、複数のテナントにわたって見られるパターンおよび脅威インテリジェンスを使用します。その際に、[プライバシーに関する声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)で説明されている誓約に従います。

## <a name="data-location"></a>データの場所
**ストレージ アカウント**: ストレージ アカウントは、仮想マシンが実行されているリージョンごとに指定されます。 そうすることで、データの収集元となる仮想マシンと同じリージョンにデータを格納することができます。 このデータは、クラッシュ ダンプ ファイルを含んでおり、ストレージ アカウントに永続的に格納されます。 VM ディスクのスナップショットは、VM ディスクと同じストレージ アカウントに格納されます。

**Azure Security Center ストレージ**: パートナーの警告、推奨事項、セキュリティの正常性状態を含むセキュリティ警告に関する情報は、現在米国内に一元的に保存されます。 この情報には、仮想マシンから収集された関連の構成情報やセキュリティ イベントが含まれる場合があります。これらは、セキュリティ警告、推奨事項、またはセキュリティの正常性状態をユーザーに提供するために必要な情報です。

Azure Security Center は、クラッシュ ダンプ ファイルの一時的なコピーを収集し、悪用の試行と成功した侵害の証拠がないか分析します。 Azure Security Center は、この分析をワークスペースと同じ geo 内で行い、分析が完了すると、一時的なコピーを削除します。

マシン アーティファクトは、VM と同じリージョンに一元的に格納されます。


## <a name="managing-data-collection-from-virtual-machines"></a>仮想マシンからのデータ収集の管理
Azure Security Center を有効にすると、各サブスクリプションのデータ収集が有効になります。 Azure Security Center の [セキュリティ ポリシー] セクションでデータ収集を有効にすることもできます。 データ収集が有効になっている場合、Azure Security Center は、サポートされている既存の全仮想マシンと新規に作成される仮想マシンに、Azure Monitoring Agent をプロビジョニングします。 Azure セキュリティ監視拡張機能は、さまざまなセキュリティ関連の構成をスキャンして、[Windows イベント トレーシング](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) トレースにイベント化します。 さらに、オペレーティング システムは、マシンの実行中にイベント ログ イベントを発生させます。 このようなデータの例として、オペレーティング システムの種類とバージョン、オペレーティング システムのログ (Windows イベント ログ)、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。 Azure Monitoring Agent は、イベント ログ エントリと ETW トレースを読み取り、分析のためにそれらをストレージ アカウントにコピーします。

仮想マシンからのデータの収集はいつでも無効にすることができます。無効にすると、前に Azure Security Center によってインストールされた Monitoring Agent がすべて削除されます。 データ収集が無効になっていても、VM ディスクのスナップショットとアーティファクトの収集は引き続き有効になります。


## <a name="see-also"></a>関連項目
この記事では、Azure Security Center でデータがどのように管理および保護されているかについて説明しました。 Azure Security Center の詳細については、以下の記事を参照してください。

* [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md) 」 -- Azure Security Center を導入するための設計上の考慮事項を計画および理解する方法について説明します。
* [Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md) 」 -- Azure リソースの正常性を監視する方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」 -- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md) 」 -- パートナー ソリューションの正常性状態を監視する方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

