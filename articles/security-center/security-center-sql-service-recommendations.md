---
title: Azure Security Center での Azure SQL サービスとデータの保護 | Microsoft Docs
description: このドキュメントでは、Azure Security Center での推奨事項に従ってデータと Azure SQL サービスを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 479e6d222c9fc7d007252bab12e807ec337ea9e6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880782"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Azure Security Center での Azure SQL サービスとデータの保護
Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。 潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。  推奨事項は、仮想マシン (VM)、ネットワーク、SQL、データ、アプリケーションといった、Azure のリソースの種類に適用されます。


### <a name="monitor-data-security"></a>データのセキュリティの監視

**[防止]** セクションの **[データのセキュリティ]** をクリックすると、**[データ リソース]** が開き、SQL とストレージの推奨事項が表示されます。 また、データベースの全般的な正常性状態に関する [推奨事項](security-center-sql-service-recommendations.md) も示されます。 ストレージの暗号化の詳細については、「[Enable encryption for Azure storage account in Azure Security Center (Azure Security Center における Azure ストレージ アカウントの暗号化の有効化)](security-center-enable-encryption-for-storage-account.md)」を参照してください。

![データ リソース](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

**[SQL Recommendations (SQL の推奨事項)]** で任意の推奨事項をクリックすると、問題解決のためのアクションの詳細を確認できます。 次の例では、**[Database Auditing & Threat detection on SQL databases (データベースの監査と SQL Database の脅威の検出)]** という推奨事項を展開した状態を示しています。

![SQL の推奨事項の詳細](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

**[SQL データベースの監査と脅威検出を有効にする]** には、次の情報が表示されます。

* SQL データベースの一覧
* SQL データベースが配置されているサーバー
* この設定がサーバーから継承されたものか、このデータベースに固有の設定かに関する情報
* 現在の状態
* 問題の重大度

この推奨事項に対処するためにデータベースをクリックすると、以下に示す **[監査と脅威検出]** が開きます。

![監査と脅威検出](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

監査を有効にするには、**[監査]** オプションの **[オン]** を選択します。

## <a name="data-and-storage-recommendations"></a>データとストレージの推奨事項

|リソースの種類|セキュリティ スコア|推奨|説明|
|----|----|----|----|
|ストレージ アカウント|20|ストレージ アカウントへの安全な転送を必須にする|安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します。|
|Redis|20|Azure Cache for Redis に対してセキュリティで保護された接続のみを有効にする|Azure Cache for Redis に対して SSL 経由の接続のみを有効にします。 セキュリティで保護された接続を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します。|
|SQL|15|SQL データベースで Transparent Data Encryption を有効にする|Transparent Data Encryption を有効にすることで、保存データを保護し、コンプライアンス要件を満たします。|
|SQL|15|SQL サーバーの監査の有効化|Azure SQL サーバーの監査を有効にします。 (Azure SQL サービスのみ。 仮想マシン上で実行されている SQL は含まれません)。|
|SQL|15|SQL データベースの監査の有効化|Azure SQL データベースの監査を有効にします。 (Azure SQL サービスのみ。 仮想マシン上で実行されている SQL は含まれません)。|
|Data Lake Analytics|15|Data Lake Analytics の保存時の暗号化を有効にする|Transparent Data Encryption を有効にして、Data Lake Analytics で保存データをセキュリティで保護します。 保存時の暗号化は透過的です。つまり、Data Lake Analytics によって自動的に、データが保存される前に暗号化され、取得される前に解読されます。 Data Lake Analytics と連携するアプリケーションやサービスに、暗号化に伴う変更を加える必要はありません。 保存時の暗号化により、物理的な盗難からデータ損失のリスクを最小限に抑え、法令遵守の要件を満たすことにも役立ちます。|
|Data Lake Store|15|Data Lake Store の保存時の暗号化を有効にする|Transparent Data Encryption を有効にして、Data Lake Store で保存データをセキュリティで保護します。 保存時の暗号化は透過的です。つまり、Data Lake Store によって自動的に、データが保存される前に暗号化され、取得される前に解読されます。 暗号化に対応するために Data Lake Store と連携するアプリケーションやサービスに変更を加える必要はありません。 保存時の暗号化により、物理的な盗難からデータ損失のリスクを最小限に抑え、法令遵守の要件を満たすことにも役立ちます。|
|Data Lake Analytics|5|Data Lake Analytics で診断ログを有効にする|ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |
|Data Lake Store|5|Azure Data Lake Store で診断ログを有効にする|ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |
|SQL|30|SQL データベースの脆弱性を修復する|SQL の脆弱性評価では、データベースのセキュリティの脆弱性をスキャンすることで、構成の間違い、過剰なアクセス許可、機密性の高いデータの未保護など、ベスト プラクティスからのすべての逸脱を明らかにします。 見つかった脆弱性を解決すると、データベースのセキュリティ水準が大幅に向上する可能性があります。|
|SQL|20|SQL Server に Azure AD 管理者をプロビジョニングする|SQL Server に Azure AD 管理者をプロビジョニングして、Azure AD 認証を有効にします。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。|
|ストレージ アカウント|15|ストレージ アカウントへの無制限のネットワーク アクセスを無効にする|ストレージ アカウントのファイアウォール設定で無制限のネットワーク アクセスを監査します。 代わりに、許可されているネットワークからのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するには、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます。|
|ストレージ アカウント|1|Azure Resource Manager モデルのリソースにストレージ アカウントを移行する|新しい Azure Resource Manager V2 をストレージ アカウントに使用して、セキュリティの拡張機能を提供します。たとえば、アクセス制御の強化 (RBAC)、監査の改善、Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレットのためのキー コンテナーへのアクセス、Azure AD に基づく認証、セキュリティ管理を容易にするタグとリソース グループのサポートがあります。|

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

* [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
* [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
