---
title: "Operations Management Suite のセキュリティと監査ソリューションのデータ セキュリティ | Microsoft Docs"
description: "このドキュメントでは、Operations Management Suite のセキュリティと監査ソリューションでデータがどのように管理、保護されているかについて説明します。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: 5001cd47b6ee51967d1286414ccefedd8e7e7813
ms.openlocfilehash: 6af6c10cef317453131197db74a9380518afadf0
ms.lasthandoff: 12/02/2016


---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Operations Management Suite のセキュリティと監査ソリューションのデータ セキュリティ
ユーザーが脅威を回避、検出、対応できるように、[Operations Management Suite (OMS) のセキュリティと監視ソリューション](operations-management-suite-overview.md)は、次のような、リソースに関するデータを収集して処理します。

* セキュリティ イベント ログ
* Windows イベント トレーシング (ETW) イベント
* AppLocker の監査イベント
* Windows ファイアウォール ログ
* Advanced Threat Analytics のイベント
* ベースライン評価の結果
* マルウェア対策評価の結果
* 更新プログラム/パッチ評価の結果
* エージェントで明示的に有効になっている Syslog ストリーム

このデータのプライバシーとセキュリティは厳密に保護されています。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。
この記事では、OMS のセキュリティと監査ソリューションでデータがどのように管理、保護されているかを説明します。

## <a name="data-sources"></a>データ ソース
OMS のセキュリティと監査ソリューションでは、OMS エージェントがインストールされている仮想マシンと物理コンピューターのデータを分析します。 OMS のセキュリティと監査ソリューションは、Windows のイベント、監査ログ、IIS ログ、syslog メッセージなど、セキュリティ イベントに関する構成情報を収集できます。 このようなデータの例として、オペレーティング システムの種類とバージョン、実行中のプロセス、コンピューター名、IP アドレス、ログイン ユーザー、テナント ID などがあります。  

## <a name="data-protection"></a>データ保護
**データの分離**: データはサービス全体を通じて、各コンポーネントに論理的に分割されて保存されます。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。 

**データ アクセス**: セキュリティの推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Microsoft の担当者は、サービスによって収集または分析された情報にアクセスする場合があります。 Microsoft は、[Microsoft オンライン サービスの使用条件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)と[プライバシーに関する声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)を遵守しています。これらの文書には、Microsoft が宣伝や他の商業上の目的のために顧客データを使用したり、そのデータから情報を取り出したりすることはないと記載されています。 セキュリティの推奨事項を提供したり、潜在的なセキュリティの脅威を調査したりするために、Microsoft の担当者は、サービスによって収集または分析された情報にアクセスする場合があります。 ユーザーに Azure サービスを提供するため、およびそれに準じた目的のためにだけ、顧客データを使用します。 お客様が所有するデータに対するすべての権限は、お客様が保持します。

**データの使用**: Microsoft は、防止と検出の機能を強化するために、複数のテナントにわたって見られるパターンおよび脅威インテリジェンスを使用します。その際に、[プライバシーに関する声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)で説明されている誓約に従います。

> [!NOTE]
> データの場所は、ワークスペースの作成中に OMS ワークスペース レベルで構成されます。これは、最初の OMS のセキュリティと監査の構成プロセスの一環です。
> 
> 

## <a name="see-also"></a>関連項目
この記事では、OMS でデータがどのように管理および保護されているかについて説明しました。 OMS のセキュリティと監査ソリューションの詳細については、次を参照してください。

* [Operations Management Suite (OMS) overview (Operations Management Suite (OMS) の概要)](operations-management-suite-overview.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのセキュリティの警告に対する監視と対応](oms-security-responding-alerts.md)
* [Operations Management Suite のセキュリティと監査ソリューションでのリソースの監視](oms-security-monitoring-resources.md)


