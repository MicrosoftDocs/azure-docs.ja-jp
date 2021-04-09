---
title: Azure Active Directory の Enterprise State Roaming とは
description: Enterprise State Roaming は、複数の Windows デバイス全体で統一されたエクスペリエンスをユーザーに提供します
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "77194281"
---
# <a name="what-is-enterprise-state-roaming"></a>Enterprise State Roaming とは

Windows 10 では、 [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) のユーザーが、そのユーザー設定とアプリケーション設定のデータをクラウドと安全に同期させることができます。 Enterprise State Roaming によって複数の Windows デバイスの使用環境が統一され、新しいデバイスを構成するために必要な時間が短縮されます。 Enterprise State Roaming の働きは、Windows 8 で初めて導入された標準的な [コンシューマー設定の同期機能](https://go.microsoft.com/fwlink/?linkid=2015135) と似ています。 それに加えて、Enterprise State Roaming には次の機能が用意されています。

* **企業データとコンシューマー データの分離 – 組織** のデータはその組織の管理下に置かれます。コンシューマー クラウド アカウントに企業データが混入したり、エンタープライズ クラウド アカウントにコンシューマー データが混入したりすることはありません。
* **セキュリティの強化** – データはユーザーの Windows 10 デバイスから送信される前に、Azure Rights Management (Azure RMS) を使って自動的に暗号化されます。クラウドには、暗号化された状態のままデータが保存されます。 名前空間 (設定の名前や Windows アプリの名前) を除き、コンテンツはすべて暗号化された状態でクラウドに保存されます。  
* **管理と監視の向上** – Azure AD ポータルの統合により、組織内のだれがどのデバイスの設定を同期させているかを管理および表示できます。 

複数の Azure リージョンで Enterprise State Roaming を使用できます。 利用できるリージョンの更新された一覧については、Azure Active Directory の [リージョン別の Azure サービス](https://azure.microsoft.com/regions/#services) に関するページをご覧ください。

| [アーティクル] | 説明 |
| --- | --- |
| [Azure Active Directory の Enterprise State Roaming を有効にする](enterprise-state-roaming-enable.md) |Enterprise State Roaming は、Azure Active Directory (Azure AD) の Premium サブスクリプションを所有しているすべての組織が利用できます。 Azure AD サブスクリプションの取得方法の詳細については、[Azure AD の製品ページ](https://azure.microsoft.com/services/active-directory)を参照してください。 |
| [設定とデータのローミングに関する FAQ](enterprise-state-roaming-faqs.md) |この記事では、IT 管理者が設定やアプリ データの同期に関して抱く可能性のあるいくつかの疑問に答えます。 |
| [設定を同期させるためのグループ ポリシーと MDM の設定](enterprise-state-roaming-group-policy-settings.md) |Windows 10 では、グループ ポリシーとモバイル デバイス管理 (MDM) のポリシー設定を通じて、設定の同期を制限することができます。 |
| [Windows 10 ローミング設定リファレンス](enterprise-state-roaming-windows-settings-reference.md) |Windows 10 でローミングまたはバックアップされる設定の一覧です。 |
| [トラブルシューティング](enterprise-state-roaming-troubleshooting.md) |この記事では、トラブルシューティングの基本的な手順について説明します。既知の問題の一覧も含まれています。 |

## <a name="next-steps"></a>次のステップ

Enterprise State Roaming を有効にする方法については、「[Enterprise State Roaming を有効にする](enterprise-state-roaming-enable.md)」を参照してください。
