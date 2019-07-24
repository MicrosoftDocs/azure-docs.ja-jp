---
title: LinkedIn アカウント接続に対する管理者の同意 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Microsoft アプリの LinkedIn 統合アカウント接続を有効または無効にする方法について説明します
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/21/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e07c53192ea2c8b792256af944c81c9c909dc55
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368128"
---
# <a name="consent-to-linkedin-account-connections-for-an-azure-active-directory-organization"></a>Azure Active Directory 組織の LinkedIn アカウント接続に同意する

組織内のユーザーに、一部の Microsoft アプリ内で自分の LinkedIn 接続へのアクセスを許可することができます。 ユーザーが自分のアカウントへの接続に同意するまで、データは共有されません。 ご自分の組織を Azure Active Directory (Azure AD) [管理センター](https://aad.portal.azure.com)に統合できます。

> [!IMPORTANT]
> LinkedIn アカウント接続の設定は、現在 Azure AD 組織にロールアウト中です。 組織にロールアウトされると、既定で有効になります。
> 
> 例外:
> * 設定は、米国政府機関用 Microsoft Cloud、Microsoft Cloud Germany、または中国の 21 vianet によって運用される Azure および Office 365 を使用しているお客様はご利用いただけません。
> * 設定は、ドイツでプロビジョニングされたテナントの既定によりオフになります。 Microsoft Cloud Germany を使用しているお客様は設定を使用できませんのでご注意ください。
> * 設定は、フランスでプロビジョニングされたテナントの既定によりオフになります。
>
> 組織で LinkedIn アカウント接続が有効になると、アプリがユーザーに代わって会社のデータにアクセスすることにユーザーが同意した後にアカウント接続が機能します。 ユーザーの同意設定については、「[アプリケーションへのユーザー アクセスの削除方法](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)」を参照してください。

## <a name="use-the-azure-portal-to-enable-linkedin-account-connections"></a>Azure portal を使用して LinkedIn アカウント接続を有効にする

組織全体に対してでも、組織内の選択したユーザーのみに対してでも、アクセス権を付与したいユーザーにのみ LinkedIn アカウント接続を有効にできます。

1. Azure AD 組織のグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com/)にサインインします。
2. **[ユーザー]** を選択します。
3. **[ユーザー]** ウィンドウで、**[ユーザー設定]** を選択します。
4. **[LinkedIn アカウント接続]** で、ユーザーが自分のアカウントに接続して一部の Microsoft アプリ内で自分の LinkedIn 接続にアクセスすることを許可します。 ユーザーが自分のアカウントへの接続に同意するまで、データは共有されません。

  * 組織内のすべてのユーザーに対してサービスに同意するには、**[はい]** を選択します
  * 組織内の選択したユーザーのみに対して同意するには、**[選択済み]** を選択します。
  * 組織内のユーザーに対する同意を取り下げるには、**[いいえ]** を選択します

    ![組織の LinkedIn アカウント接続を統合する](./media/linkedin-integration/linkedin-integration.png)

5. 完了したら、**[保存]** を選択して設定を保存します。
     
## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>グループ ポリシーを使用して LinkedIn アカウント接続を有効にする

1. [Office 2016 管理用テンプレート ファイル (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) をダウンロードします。
2. **ADMX** ファイルを抽出して中央のストアにコピーします。
3. [グループ ポリシーの管理] を開きます。
4. 次の設定を使用してグループ ポリシー オブジェクトを作成します。**[ユーザーの構成]** > **[管理用テンプレート]** > **[Microsoft Office 2016]** > **[その他]** > **[LinkedIn の機能を Office アプリケーションで表示]**。
5. **[有効]** または **[無効]** を選択します。
  
   状態 | 効果
   ------ | ------
   **有効** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が有効になります。 組織内のユーザーは、各自の Office 2016 アプリケーションで LinkedIn の機能を使用できます。
   **Disabled** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が無効になり、エンドユーザーはこの設定を変更できません。 組織内のユーザーがその Office 2016 アプリケーションで LinkedIn の機能を使用することはできません。

このグループ ポリシーが適用されるのは、ローカル コンピューターの Office 2016 アプリだけです。 ユーザーが各自の Office 2016 アプリで LinkedIn を無効にした場合でも、Office 365 で LinkedIn の機能が表示されます。

## <a name="next-steps"></a>次の手順

* [LinkedIn のユーザーの同意とデータ共有](linkedin-user-consent.md)

* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

* [Azure portal で現在の LinkedIn の統合設定を確認する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
