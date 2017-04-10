
---
title: "Azure Active Directory の Enterprise State Roaming を有効にする | Microsoft Docs"
description: "Windows デバイスの Enterprise State Roaming の設定に関してよく寄せられる質問について取り上げます。 Enterprise State Roaming によって複数の Windows デバイスの使用環境が統一され、新しいデバイスを構成するために必要な時間が短縮されます。"
services: active-directory
keywords: "Enterprise State Roaming, Windows クラウド, Enterprise State Roaming を有効にする方法"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 0628dafc9cc4cb05e678f741d913e00116013459
ms.openlocfilehash: 435f801b254a6c423a6ec3e96037ade62e14af29
ms.lasthandoff: 01/05/2017


---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory の Enterprise State Roaming を有効にする
Enterprise State Roaming は、Azure Active Directory (Azure AD) の Premium サブスクリプションを所有しているすべての組織が利用できます。 Azure AD サブスクリプションの取得方法について詳しくは、 [Azure AD の製品ページ](https://azure.microsoft.com/services/active-directory)を参照してください。

Enterprise State Roaming を有効にすると、Azure Rights Management の無料、制限付き使用サブスクリプションのライセンスが自動的に付与されます。 この無料サブスクリプションは、Enterprise State Roaming サービスで同期されたエンタープライズ設定データおよびアプリケーション データの暗号化と暗号化の解除に限定されます。Azure Rights Management の全機能を使用するためには有料サブスクリプションが必要です。

Premium Azure AD サブスクリプションの取得後、次の手順に従って Enterprise State Roaming を有効にします。

1. Azure クラシック ポータルにログインします。
2. 左側にある **[Active Directory]**を選択し、Enterprise State Roaming を有効にするディレクトリを選択します。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming.png)
3. 上部にある **[構成]** タブに移動します。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-configure.png)
4. ページを下にスクロールし、**[設定とエンタープライズ アプリ データの同期が許可されるユーザー]** を選択して **[保存]** をクリックします。
   ![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-select-all-sync-settings.png)

Windows 10 デバイスで Enterprise State Roaming サービスを使って設定をローミングするためには、そのデバイスを Azure AD の ID で認証する必要があります。 デバイスが Azure AD に参加している場合、そのユーザーの主要なログインは Azure AD の ID です。追加の構成は必要ありません。 デバイスが従来のオンプレミスの Active Directory を使用している場合、IT 管理者は[Windows 10 エクスペリエンスのためにドメイン参加済みデバイスを Azure AD に接続する](active-directory-azureadjoin-devices-group-policy.md)必要があります。

## <a name="sync-data-storage"></a>データ ストレージの同期
Enterprise State Roaming のデータは、Azure Active Directory のインスタンスに設定されている国/リージョンの値と最も適合する 1 つ以上の [Azure リージョン](https://azure.microsoft.com/regions/) でホストされます。 Enterprise State Roaming データは、北米、EMEA、および APAC という主な 3 つの地域リージョンに基づいてパーティション分割されます。 テナントの Enterprise State Roaming データは、地域リージョンでローカルに配置されますが、リージョン間ではレプリケートされません。  たとえば、国/リージョンの値が "France" または "Zambia” のような EMEA 諸国のいずれかに設定されている顧客のデータは、ヨーロッパ内の 1 つ以上の Azure リージョンでホストされます。  Azure AD で国/リージョンの値が "United States" または “Canada” のような北米諸国のいずれかに設定されている顧客のデータは、米国内の 1 つ以上の Azure リージョンでホストされます。  Azure AD で国/リージョンの値が "Australia" または “New Zealand” のような APAC 諸国のいずれかに設定されている顧客のデータは、アジア内の 1 つ以上の Azure リージョンでホストされます。  南米諸国と南極大陸のデータは、米国内の 1 つ以上の Azure リージョンでホストされます。  国/リージョンの値は Azure AD ディレクトリの作成プロセスで設定され、後から変更することはできません。 

データの保管場所についてさらに詳しい情報が必要な場合は、 [Azure サポート](https://azure.microsoft.com/support/options/)にチケットを提出してください。

## <a name="manage-enterprise-state-roaming"></a>Enterprise State Roaming の管理
Enterprise State Roaming は、Azure AD グローバル管理者が Azure クラシック ポータルから有効にしたり無効にしたりすることができます。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-manage.png)

グローバル管理者は、設定の同期を特定のセキュリティ グループに制限できます。

加えてグローバル管理者は、ユーザーごとのデバイス同期ステータス レポートを閲覧できます。閲覧するには、Active Directory インスタンス **[ユーザー]** 一覧で特定のユーザーを選択して **[デバイス]** タブをクリックし、ビュー **[設定とエンタープライズ アプリ データを同期しているデバイス]** を選択します。
![](./media/active-directory-enterprise-state-roaming/active-directory-enterprise-state-roaming-device-sync-settings.png)

## <a name="data-retention"></a>データの保持
Azure との間で Enterprise State Roaming を介して同期されるデータは、手動で削除されるか対象となるデータが古いと判断されるまで無期限に保持されます。 

**明示的な削除:** Azure 管理者がユーザーまたはディレクトリを削除するか、管理者がデータを削除することを明示的に要求すると、データは削除されます。

* **ユーザーの削除**: Azure AD でユーザーを削除した場合、ユーザー アカウント ローミング データは削除対象としてマークされ、90 ～ 180 日間で削除されます。 
* **ディレクトリの削除**: Azure AD からディレクトリ全体を削除した場合は、その操作が直ちに実行されます。 そのディレクトリに関連付けられたすべての設定データは削除対象としてマークされ、90 ～ 180 日間で削除されます。 
* **要求による削除**: Azure AD 管理者の方は、特定のユーザーのデータまたは設定データを手動で削除する必要がある場合、 [Azure サポート](https://azure.microsoft.com/support/)にチケットを提出してください。 

**古いデータの削除**: 1 年間 ("リテンション期間") にわたってアクセスされていないデータは古いデータと見なされ、Azure から削除される場合があります。 リテンション期間は変更されることがありますが、90 日未満になることはありません。 Windows またはアプリケーションの特定の設定のまとまりや、ユーザーの全設定が古いデータと見なされることもあります。 次に例を示します。

* 特定の設定全体にデバイスからのアクセスがない場合 (アプリケーションがデバイスから削除された場合や、特定のユーザーの全デバイスで "テーマ" などの設定グループが無効にされた場合など)、リテンション期間の経過後その設定全体が古いと見なされ、削除される可能性があります。 
* ユーザーがそのすべてのデバイスで設定の同期をオフにした場合、いずれの設定データもアクセスされなくなり、リテンション期間の経過後にそのユーザーのすべての設定データが古いと見なされ、削除される可能性があります。 
* Azure AD ディレクトリ管理者がディレクトリ全体の Enterprise State Roaming をオフにした場合、そのディレクトリ内のすべてのユーザーについて設定の同期が中止され、リテンション期間後すべてのユーザーの全設定データが古いデータとなって、削除される可能性があります。 

**削除されたデータの復元**: データの保持ポリシーを設定で変更することはできません。 一度完全に削除されたデータは復元できなくなります。 ただし、設定データが削除されるのは Azure からのみであることに注意してください。エンド ユーザーのデバイスからは削除されません。 その後いずれかのデバイスから Enterprise State Roaming サービスに再接続すると、設定が再同期されて Azure に保存されます。

## <a name="related-topics"></a>関連トピック
* [Enterprise State Roaming の概要](active-directory-windows-enterprise-state-roaming-overview.md)
* [設定とデータのローミングに関する FAQ](active-directory-windows-enterprise-state-roaming-faqs.md)
* [設定を同期させるためのグループ ポリシーと MDM の設定](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 ローミング設定リファレンス](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [トラブルシューティング](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

