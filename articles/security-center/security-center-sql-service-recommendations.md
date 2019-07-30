---
title: Azure Security Center で Azure データと記憶域サービスを保護する | Microsoft Docs
description: このドキュメントでは、Azure Security Center での推奨事項に従ってデータと Azure SQL サービスを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/19/2019
ms.author: v-mohabe
ms.openlocfilehash: 9e48114d0d4159d40006710f9c8194dea0d775f8
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295631"
---
# <a name="protect-azure-data-and-storage-services-in-azure-security-center"></a>Azure Security Center で Azure データと記憶域サービスを保護する
このトピックでは、データと記憶域リソースのセキュリティに関する推奨事項を表示および実装する方法を示します。 Azure Security Center は、Azure リソースのセキュリティ状態を分析する際に、セキュリティに関する推奨事項を検出します。

## <a name="view-your-data-security-information"></a>データのセキュリティ情報を表示する

1. **[リソース セキュリティの検疫]** セクションで、 **[Data and storage resources] (データと記憶域のリソース)** をクリックします。

   ![データと記憶域のリソース](./media/security-center-monitoring/click-data.png)

    **[Data security] (データ セキュリティ)** ページとデータ リソースの推奨事項が開きます。

     ![データ リソース](./media/security-center-monitoring/sql-overview.png)

このページからは、次のことを行うことができます。

* **[概要]** タブをクリックすると、修復するすべてのデータ リソースの推奨事項が一覧表示されます。 
* 各タブをクリックし、リソースの種類ごとに推奨事項を表示します。

    > [!NOTE]
    > ストレージの暗号化の詳細については、「[Enable encryption for Azure storage account in Azure Security Center (Azure Security Center における Azure ストレージ アカウントの暗号化の有効化)](security-center-enable-encryption-for-storage-account.md)」を参照してください。


## <a name="remediate-a-recommendation-on-a-data-resource"></a>データ リソースの推奨事項を修復する

1. いずれかのリソース タブから、リソースをクリックします。 情報ページが開き、修復する推奨事項の一覧が表示されます。

    ![リソース情報](./media/security-center-monitoring/sql-recommendations.png)

2. 推奨事項をクリックします。 推奨事項ページが開き、推奨事項を実装する **[修復手順]** が表示されます。

   ![修復手順](./media/security-center-monitoring/remediate1.png)

3. **[アクションの実行]** をクリックします。 リソースの設定ページが表示されます。

    ![推奨事項を有効にする](./media/security-center-monitoring/remediate2.png)

4. **[修復手順]** に従い、 **[保存]** をクリックします。

## <a name="data-and-storage-recommendations"></a>データとストレージの推奨事項

|リソースの種類|セキュリティ スコア|推奨|説明|
|----|----|----|----|
|ストレージ アカウント|20|ストレージ アカウントへの安全な転送を有効にする必要がある|安全な転送は、ストレージ アカウントに、セキュリティで保護された接続 (HTTPS) からの要求のみを受け入れるように強制するオプションです。 HTTPS によりサーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します。|
|Redis|20|Redis Cache に対してセキュリティで保護された接続のみを有効にする必要がある|Azure Cache for Redis に対して SSL 経由の接続のみを有効にします。 セキュリティで保護された接続を使用することにより、サーバーとサービス間の認証が確実に行われ、転送中のデータをネットワーク層の攻撃 (man-in-the-middle、傍受、セッション ハイジャックなど) から保護します。|
|SQL|15|SQL データベースで Transparent Data Encryption を有効にする必要がある|Transparent Data Encryption を有効にすることで、保存データを保護し、コンプライアンス要件を満たします。|
|SQL|15|SQL サーバー監査を有効にする必要がある|Azure SQL サーバーの監査を有効にします。 (Azure SQL サービスのみ。 仮想マシン上で実行されている SQL は含まれません)。|
|Data Lake Analytics|5|Data Lake Analytics で診断ログを有効にする必要がある|ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |
|Data Lake Store|5|Azure Data Lake Store で診断ログを有効にする必要がある|ログを有効にし、それらを最大 1 年間保持します。 これにより、セキュリティ インシデントが発生した場合やネットワークが侵害された場合に、調査目的でアクティビティ証跡を再作成できます。 |
|SQL|30|SQL データベースの脆弱性を修復する必要がある|SQL の脆弱性評価では、データベースのセキュリティの脆弱性をスキャンすることで、構成の間違い、過剰なアクセス許可、機密性の高いデータの未保護など、ベスト プラクティスからのすべての逸脱を明らかにします。 見つかった脆弱性を解決すると、データベースのセキュリティ水準が大幅に向上する可能性があります。|
|SQL|20|SQL Server に Azure AD 管理者をプロビジョニングする|SQL Server に Azure AD 管理者をプロビジョニングして、Azure AD 認証を有効にします。 Azure AD 認証を使用して、アクセス許可の管理を簡単にし、データベース ユーザーとその他の Microsoft サービスの ID を一元管理できます。|
|ストレージ アカウント|15|ファイアウォールと仮想ネットワークの構成があるストレージ アカウントへのアクセスを制限する必要がある|ストレージ アカウントのファイアウォール設定で無制限のネットワーク アクセスを監査します。 代わりに、許可されているネットワークからのアプリケーションのみがストレージ アカウントにアクセスできるように、ネットワーク ルールを構成します。 インターネットまたはオンプレミスの特定のクライアントからの接続を許可するには、特定の Azure 仮想ネットワークからのトラフィックまたはパブリック インターネット IP アドレス範囲に、アクセス権を付与できます。|
|ストレージ アカウント|1|ストレージ アカウントを新しい Azure Resource Manager リソースに移行する必要がある|新しい Azure Resource Manager V2 をストレージ アカウントに使用して、セキュリティの拡張機能を提供します。たとえば、アクセス制御の強化 (RBAC)、監査の改善、Resource Manager ベースのデプロイとガバナンス、マネージド ID へのアクセス、シークレットのためのキー コンテナーへのアクセス、Azure AD に基づく認証、セキュリティ管理を容易にするタグとリソース グループのサポートがあります。|

## <a name="see-also"></a>関連項目
その他の Azure リソースの種類に適用される推奨事項の詳細については、次のトピックをご覧ください。

* [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
* [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
* [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)

Security Center の詳細については、次のトピックを参照してください。

* [Azure Security Center でのセキュリティ ポリシーの設定](tutorial-security-policy.md) 」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
* [Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md) 」-- セキュリティの警告の管理と対応の方法について説明しています。
* [Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md) 」-- このサービスの使用に関してよく寄せられる質問が記載されています。
