---
title: Azure Security Center と Azure SQL Database サービス | Microsoft Docs
description: この記事では、Azure SQL Database に存在するデータベースのセキュリティに Security Center を活かす方法について説明します。
services: sql-database
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f109adfd-daed-4257-9692-2042a1399480
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: rkarlin
ms.openlocfilehash: 0a889de79b6a5921007614dac8d610c1be0222d2
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114090"
---
# <a name="azure-security-center-and-azure-sql-database-service"></a>Azure Security Center と Azure SQL Database サービス
[Azure Security Center](https://azure.microsoft.com/documentation/services/security-center/) は、脅威の防御、検出、対応を可能にする機能です。 これにより、Azure サブスクリプション全体に統合セキュリティの監視とポリシーの管理を提供し、気付かない可能性がある脅威を検出し、セキュリティ ソリューションの広範なエコシステムと連動します。

この記事では、Azure SQL Database に存在するデータベースのセキュリティに Security Center を活かす方法について説明します。

## <a name="why-use-security-center"></a>Security Center を使う意義
すべてのサーバーとデータベースのセキュリティを Security Center で可視化することによって、SQL Database に格納されているデータを保護することができます。 Security Center では、次のことを実行できます。

* SQL Database の暗号化と監査のポリシーを定義する。
* 利用しているすべてのサブスクリプションを対象に SQL Database リソースのセキュリティを監視する。
* セキュリティの問題をすばやく特定して修復する。
* [Azure SQL Database 脅威検出](../sql-database/sql-database-threat-detection.md)からのアラートを統合する。

Security Center の機能は、SQL Database のリソースを保護することだけではありません。Azure Virtual Machines、Cloud Services、App Services、Virtual Network などのセキュリティを監視し、必要な対応を行うことができます。 Security Center の詳細については、[こちら](security-center-intro.md)を参照してください。

## <a name="prerequisites"></a>前提条件
セキュリティ センターを使用するには、Microsoft Azure のサブスクリプションが必要です。 サブスクリプションがあれば、Security Center の Free レベルが有効になっています。 Security Center の Free レベルと Standard レベルの詳細については、「[セキュリティ センターの価格](https://azure.microsoft.com/pricing/details/security-center/)」を参照してください。

セキュリティ センターは、ロール ベースのアクセスをサポートしています。 ロールベースのアクセス制御 (RBAC) の詳細については、「 [Azure Active Directory のロール ベースのアクセス制御](../role-based-access-control/role-assignments-portal.md)」を参照してください。 Security Center の FAQ に、[Security Center でのアクセス許可の処理](security-center-faq.md#permissions)についての情報が提供されています。

## <a name="access-security-center"></a>セキュリティ センターへのアクセス
Security Center には [Azure ポータル](https://azure.microsoft.com/features/azure-portal/)からアクセスします。 [ポータルにサインイン](https://portal.azure.com/)して **[セキュリティ センター] オプション**を選択します。

![[セキュリティ センター] オプション][1]

**[セキュリティ センター]** ブレードが開きます。
![[セキュリティ センター] ブレード][2]

## <a name="set-security-policy"></a>セキュリティ ポリシーの設定
セキュリティ ポリシーは、指定されたサブスクリプションまたはリソース グループ内のリソースに推奨されるコントロールのセットを定義します。 Security Center では、セキュリティに関する会社のニーズ、および各サブスクリプションでのアプリケーションの種類やデータの機密度に合わせて、サブスクリプションまたはリソース グループのポリシーを定義できます。

ポリシーを設定することで、SQL 監査や SQL Transparent Data Encryption (TDE) の推奨事項を表示することができます。

* **SQL 監査と脅威検出**を有効にすると、コンプライアンス、高度な検出、調査のため、Azure データベースへのアクセスの監査を有効にすることが Security Center によって提案されます。
* **SQL Transparent Data Encryption** を有効にすると、Azure SQL データベース、関連付けられたバックアップ、トランザクション ログ ファイルに関して、保存データの暗号化を有効にすることが Security Center によって提案されます。

セキュリティ ポリシーを設定するには、[セキュリティ センター] ブレードの **[ポリシー]** タイルを選択します。 セキュリティ ポリシーを有効にするサブスクリプションは、**[セキュリティ ポリシー]** ブレードで選択します。 **[防止ポリシー]** を選択し、そのサブスクリプションで使用するセキュリティの推奨事項を **[オン]** にしてください。
![セキュリティ ポリシー][3]

詳細については、[セキュリティ ポリシーの設定](tutorial-security-policy.md)に関するページを参照してください。

## <a name="manage-security-recommendation"></a>セキュリティの推奨事項への対応
セキュリティ センターは、Azure リソースのセキュリティの状態を定期的に分析します。 セキュリティ センターでは、潜在的なセキュリティの脆弱性が特定されると、推奨事項が作成されます。 推奨事項では、必要なコントロールを構成する手順を説明します。

セキュリティ ポリシーを設定すると、Security Center によってリソースのセキュリティの状態が分析され、潜在的な脆弱性が特定されます。 推奨事項は表形式で表示されます。表の行はそれぞれ特定の推奨事項を表します。 Azure SQL Database に関して提案される推奨事項とそれを適用する利点については、以下の表を参考にしてください。 推奨事項を選択すると、それを Security Center で実装する方法について説明した記事をご覧いただけます。

| 推奨 | 説明 |
| --- | --- |
| [SQL サーバーの監査と脅威検出を有効にする](security-center-enable-auditing-on-sql-servers.md) |SQL Database サーバーの監査と脅威検出を有効にするよう推奨されます。 (対象になるのは SQL Database サービスのみです。 仮想マシン上で実行されている Microsoft SQL Server は含まれません。) |
| [SQL データベースの監査と脅威検出を有効にする](security-center-enable-auditing-on-sql-databases.md) |SQL Database データベースの監査と脅威検出を有効にするよう推奨されます。 (対象になるのは SQL Database サービスのみです。 仮想マシン上で実行されている Microsoft SQL Server は含まれません。) |
| [Transparent Data Encryption を有効にする](security-center-enable-transparent-data-encryption.md) |SQL データベースの暗号化を有効にするよう推奨されます。 (対象になるのは SQL Database サービスのみです。) |

Azure リソースに関する推奨事項を確認するには、[セキュリティ センター] ブレードの **[推奨事項]** タイルを選択してください。 **[推奨事項]** ブレードで特定の推奨事項を選択すると詳細が表示されます。 この例では、**[Enable Auditing & Threat detection on SQL servers (SQL サーバーの監査と脅威検出を有効にする)]** を選択してみましょう。

![Recommendations][4]

監査と脅威検出が有効になっていない SQL サーバーが、下図のように Security Center によって示されます。 監査を有効にしたら、脅威の検出設定と、セキュリティの警告を受信する電子メールの設定を構成できます。 脅威の検出機能を使用すると、データベースのセキュリティを脅かす可能性のある異常なデータベース アクティビティが検出されたときに警告を受けることができます。 この警告は、Security Center ダッシュボードに表示されます。
![監査と脅威の検出][5]

[Azure Portal での SQL Database の脅威の検出](../sql-database/sql-database-threat-detection-portal.md)に関するページの手順に従って、脅威の検出を有効にして構成し、異常なアクティビティの検出時にセキュリティの警告を受信する電子メールの一覧を構成します。

推奨事項の詳細については、[セキュリティに関する推奨事項の管理](security-center-recommendations.md)についてのページを参照してください。

## <a name="monitor-security-health"></a>セキュリティ正常性を監視する
サブスクリプションのリソースに対して [セキュリティ ポリシー](tutorial-security-policy.md) を有効にすると、Security Center は、リソースのセキュリティを分析して潜在的な脆弱性を特定します。  リソースのセキュリティの状態は、**[リソース セキュリティの正常性]** タイルで確認できます。 **[リソース セキュリティの正常性]** タイルの **[データ]** をクリックすると、**[Data Resources (データ リソース)]** ブレードが開き、監査や Transparent Data Encryption が有効になっていないなど、SQL の問題に関する推奨事項が示されます。 また、データベースの全般的なヘルス状態に関する推奨事項も示されます。
![リソースのセキュリティ正常性][6]

詳細については、[セキュリティ ヘルスの監視](security-center-monitoring.md)に関するページを参照してください。

## <a name="manage-and-respond-to-security-alerts"></a>セキュリティの警告の管理と対応
Security Center は、真の脅威を検出し、偽陽性の検出を減らすために、[Azure SQL 脅威検出](../sql-database/sql-database-threat-detection.md)などの Azure リソースからログ データを自動的に収集、分析、統合します。 Security Center には、優先順位の付いたセキュリティの警告の一覧が表示されます。また、すぐに問題を調査する必要がある情報や、攻撃を受けたものを修復する方法についての推奨事項も表示されます。

警告を確認するには、[セキュリティ センター] ブレードの **[セキュリティ警告]** タイルを選択します。 **[セキュリティ警告]** ブレードで警告を選択すると、その原因となったイベントの詳細が表示されます。攻撃に対処するために必要な手順があれば、その情報も表示されます。 この例では、**[Potential SQL injection (SQL インジェクションの可能性)]** を選択してみましょう。
![セキュリティ警告][7]

以下に示したように、Security Center から提供される詳しい情報を確認することで、その警告を引き起こした原因、ターゲット リソース、(場合によっては) 発生元の IP アドレス、解決のための推奨事項を知ることができます。
![SQL インジェクションの可能性][8]

詳細については、[セキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順
* [Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) -- このサービスの使用に関してよく寄せられる質問が記載されています。
* [Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md) - 組織のセキュリティ要件とクラウド管理モデルに応じて Security Center の利用を最適化できる、一連の手順とタスクについて説明します。
* [Security Center のデータ セキュリティ](security-center-data-security.md) – Security Center は、構成情報、メタデータ、イベント ログ、クラッシュ ダンプ ファイルなど、Azure リソースに関するさまざまなデータを収集、処理しています。その具体的な方法について説明します。
* [セキュリティ インシデントの処理](security-center-incident.md) - Security Center でセキュリティ警告機能を使用して、セキュリティ インシデントの処理に役立てる方法を説明します。

<!--Image references-->
[1]: ./media/security-center-sql-database/security-center.png
[2]: ./media/security-center-sql-database/security-center-blade.png
[3]: ./media/security-center-sql-database/security-policy.png
[4]: ./media/security-center-sql-database/recommendation.png
[5]: ./media/security-center-sql-database/turn-on-auditing.png
[6]: ./media/security-center-sql-database/monitor-health.png
[7]: ./media/security-center-sql-database/alert.png
[8]: ./media/security-center-sql-database/sql-injection.png
