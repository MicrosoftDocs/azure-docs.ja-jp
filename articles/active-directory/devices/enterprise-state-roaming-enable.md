---
title: Azure Active Directory の Enterprise State Roaming を有効にする
description: Windows デバイスの Enterprise State Roaming の設定に関してよく寄せられる質問について取り上げます。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b554fbef63f23b3540fe49e5c45976122add25
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89268606"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Azure Active Directory の Enterprise State Roaming を有効にする

Enterprise State Roaming は、Azure AD Premium または Enterprise Mobility + Security (EMS) ライセンスを所有しているすべての組織が利用できます。 Azure AD サブスクリプションの取得方法の詳細について、[Azure AD の製品ページ](https://azure.microsoft.com/services/active-directory)を参照してください。

Enterprise State Roaming を有効にすると、組織には、Azure Information Protection の Azure Rights Management 保護を制限付きで無料使用できるライセンスが自動的に付与されます。 この無料サブスクリプションは、Enterprise State Roaming サービスで同期されたエンタープライズ設定データおよびアプリケーション データの暗号化と暗号化の解除に限定されます。 Azure Rights Management サービスのすべての機能を使用するには、[有料サブスクリプション](https://azure.microsoft.com/pricing/details/information-protection/)が必要です。

> [!NOTE]
> この記事は、2015 年 7 月に Windows 10 で提供が開始された Microsoft Edge レガシ HTML ベースのブラウザーに適用されます。 この記事は、2020 年 1 月 15 日にリリースされた新しい Microsoft Edge Chromium ベースのブラウザーには適用されません。 新しい Microsoft Edge の同期動作の詳細については、「[Microsoft Edge の同期](/deployedge/microsoft-edge-enterprise-sync)」を参照してください。

## <a name="to-enable-enterprise-state-roaming"></a>Enterprise State Roaming を有効にするには

1. [Azure AD 管理センター](https://aad.portal.azure.com/)にサインインします。
1. **[Azure Active Directory]**  >  **[デバイス]**  >  **[Enterprise State Roaming]** を選択します。
1. **[デバイス間での設定とアプリ データの同期が許可されるユーザー]** を選択します。 詳細については、[デバイス設定の構成方法](./device-management-azure-portal.md)に関するページをご覧ください。
  
   ![[デバイス間での設定とアプリ データの同期が許可されるユーザー] のデバイス設定のイメージ](./media/enterprise-state-roaming-enable/device-settings.png)
  
Windows 10 デバイスで Enterprise State Roaming サービスを使用するには、そのデバイスを Azure AD の ID で認証する必要があります。 デバイスが Azure AD に参加している場合、そのユーザーの主要なサインイン ID は Azure AD の ID です。追加の構成は必要ありません。 デバイスがオンプレミスの Active Directory を使用している場合、IT 管理者は [ハイブリッド Azure Active Directory 参加済みデバイスを構成する](./hybrid-azuread-join-plan.md)必要があります。 

## <a name="data-storage"></a>データ ストレージ

Enterprise State Roaming のデータは、Azure Active Directory のインスタンスに設定されている国/リージョンの値と最も適合する 1 つ以上の [Azure リージョン](https://azure.microsoft.com/regions/)でホストされます。 Enterprise State Roaming データは、北米、EMEA、および APAC という主な 3 つの地域リージョンに基づいてパーティション分割されます。 テナントの Enterprise State Roaming データは、地域リージョンでローカルに配置されますが、リージョン間ではレプリケートされません。  次に例を示します。

| 国/リージョンの値 | データがホストされる場所 |
| -------------------- | ------------------------ |
| EMEA の国/地域 (フランス、ザンビアなど) | ヨーロッパ内の 1 つ以上の Azure リージョン |
| 北米の国/地域 (米国、カナダなど) | 米国内の 1 つ以上の Azure リージョン |
| APAC の国/地域 (オーストラリア、ニュージーランドなど) | アジア内の 1 つ以上の Azure リージョン |
| 南アメリカ/南極地域 | 米国内の 1 つ以上の Azure リージョン |

国/リージョンの値は Azure AD ディレクトリの作成プロセスで設定され、後から変更することはできません。 データの保管場所についてさらに詳しい情報が必要な場合は、[Azure サポート](https://azure.microsoft.com/support/options/)にチケットを提出してください。

## <a name="view-per-user-device-sync-status"></a>ユーザーごとのデバイス同期状態の表示

次の手順に従って、ユーザーごとのデバイス同期状態のレポートを表示します。

1. [Azure AD 管理センター](https://aad.portal.azure.com/)にサインインします。
1. **[Azure Active Directory]**  >  **[ユーザー]**  >  **[すべてのユーザー]** の順に選択します。
1. ユーザーを選択し、 **[デバイス]** を選択します。
1. **[表示]** で、 **[Devices syncing settings and app data]\(設定やアプリ データを同期しているデバイス)** を選択して、同期状態を表示します。
  
   ![デバイス同期データ設定のイメージ](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. このユーザーに対して同期しているデバイスがある場合は、そのデバイスは次のように表示されます。
  
   ![デバイス同期列指向データのイメージ](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>データの保持

Enterprise State Roaming を使用して Microsoft クラウドと同期されたデータは、手動で削除されるまで、または該当するデータが古いデータとして判断されるまで保持されます。 

### <a name="explicit-deletion"></a>明示的な削除

明示的な削除とは、Azure 管理者がユーザーまたはディレクトリを削除するか、データを削除することを明示的に要求したときの削除です。

* **ユーザーの削除**: Azure AD でユーザーが削除された場合、ユーザー アカウント ローミング データは 90 ～ 180 日後に削除されます。 
* **ディレクトリの削除**: Azure AD からディレクトリ全体を削除した場合は、その操作が直ちに実行されます。 そのディレクトリに関連付けられた設定データはすべて、90 ～ 180 日後に削除されます。 
* **要求による削除**: Azure AD 管理者の方は、特定のユーザーのデータまたは設定データを手動で削除する必要がある場合、 [Azure サポート](https://azure.microsoft.com/support/)にチケットを提出してください。 

### <a name="stale-data-deletion"></a>古いデータの削除

1 年間 ("リテンション期間") にわたってアクセスされていないデータは古いデータと見なされ、Microsoft クラウドから削除される場合があります。 リテンション期間は変更されることがありますが、90 日未満になることはありません。 Windows またはアプリケーションの特定の設定のまとまりや、ユーザーの全設定が古いデータと見なされることもあります。 次に例を示します。

* 特定の設定全体にデバイスからのアクセスがない場合 (アプリケーションがデバイスから削除された場合や、特定のユーザーの全デバイスで "テーマ" などの設定グループが無効にされた場合など)、リテンション期間の経過後その設定全体が古いと見なされ、削除される可能性があります。 
* ユーザーがそのすべてのデバイスで設定の同期をオフにした場合、いずれの設定データもアクセスされなくなり、リテンション期間の経過後にそのユーザーのすべての設定データが古いと見なされ、削除される可能性があります。 
* Azure AD ディレクトリ管理者がディレクトリ全体の Enterprise State Roaming をオフにした場合、そのディレクトリ内のすべてのユーザーについて設定の同期が中止され、リテンション期間後すべてのユーザーの全設定データが古いデータとなって、削除される可能性があります。 

### <a name="deleted-data-recovery"></a>削除したデータの回復

データ保持ポリシーを構成することはできません。 一度完全に削除されたデータは復旧できません。 ただし、設定データが削除されるのは Microsoft クラウドからのみです。エンド ユーザーのデバイスからは削除されません。 その後、いずれかのデバイスから Enterprise State Roaming サービスに再接続すると、設定が再同期され、Microsoft クラウドに保存されます。

## <a name="next-steps"></a>次のステップ

* [Enterprise State Roaming の概要](enterprise-state-roaming-overview.md)
* [設定とデータのローミングに関する FAQ](enterprise-state-roaming-faqs.md)
* [設定を同期させるためのグループ ポリシーと MDM の設定](enterprise-state-roaming-group-policy-settings.md)
* [Windows 10 ローミング設定リファレンス](enterprise-state-roaming-windows-settings-reference.md)
* [トラブルシューティング](enterprise-state-roaming-troubleshooting.md)