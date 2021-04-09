---
title: LinkedIn アカウント接続に対する管理者の同意 - Azure AD | Microsoft Docs
description: Azure Active Directory で Microsoft アプリの LinkedIn 統合アカウント接続を有効または無効にする方法について説明します
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ffa937f91af7078b28331a655f99397d3821def
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545895"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Azure Active Directory で LinkedIn アカウント接続を統合します。

組織内のユーザーに、一部の Microsoft アプリ内で自分の LinkedIn 接続へのアクセスを許可することができます。 ユーザーが自分のアカウントへの接続に同意するまで、データは共有されません。 ご自分の組織を Azure Active Directory (Azure AD) [管理センター](https://aad.portal.azure.com)に統合できます。

> [!IMPORTANT]
> LinkedIn アカウント接続の設定は、現在 Azure AD 組織にロールアウト中です。 組織にロールアウトされると、既定で有効になります。
>
> 例外:
>
> * 本設定は、米国政府機関用 Microsoft Cloud、Microsoft Cloud Germany、または中国の 21Vianet によって運用されている Azure および Microsoft 365 を使用しているお客様はご利用いただけません。
> * ドイツにプロビジョニングされた Azure AD 組織の場合、この設定は既定でオフです。 Microsoft Cloud Germany を使用しているお客様は設定を使用できませんのでご注意ください。
> * フランスにプロビジョニングされた組織の場合、この設定は既定でオフです。
>
> 組織で LinkedIn アカウント接続が有効になると、アプリがユーザーに代わって会社のデータにアクセスすることにユーザーが同意した後にアカウント接続が機能します。 ユーザーの同意設定については、「[アプリケーションへのユーザー アクセスの削除方法](../manage-apps/methods-for-removing-user-access.md)」を参照してください。

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Azure portal での LinkedIn アカウント接続の有効化

組織全体に対してでも、組織内の選択したユーザーのみに対してでも、アクセス権を付与したいユーザーにのみ LinkedIn アカウント接続を有効にできます。

1. Azure AD 組織のグローバル管理者のアカウントで [Azure AD 管理センター](https://aad.portal.azure.com/)にサインインします。
1. **[ユーザー]** を選択します。
1. **[ユーザー]** ページで、 **[ユーザー設定]** を選択します。
1. **[LinkedIn アカウント接続]** で、ユーザーが自分のアカウントに接続して一部の Microsoft アプリ内で自分の LinkedIn 接続にアクセスすることを許可します。 ユーザーが自分のアカウントへの接続に同意するまで、データは共有されません。

    * **[はい]** を選択して組織内のすべてのユーザーに対してサービスを有効にする
    * **[選択したグループ]** を選択して、組織内の選択したユーザー グループに対してのみサービスを有効にする
    * **[いいえ]** を選択して組織内のすべてのユーザーからの同意を取り消す

    ![組織の LinkedIn アカウント接続を統合する](./media/linkedin-integration/linkedin-integration.png)

1. 完了したら、 **[保存]** を選択して設定を保存します。

> [!Important]
> LinkedIn の統合は、ユーザーがそのアカウントの接続に同意するまでは完全には有効になりません。 ユーザーのアカウント接続を有効にすると、データは共有されなくなります。

### <a name="assign-selected-users-with-a-group"></a>選択したユーザーにグループを割り当てる

ユーザーの一覧を指定する "選択" オプションは、ユーザーのグループを選択するオプションに置き換えられました。これにより多くの個別のユーザーではなく 1 つのグループに LinkedIn アカウントと Microsoft アカウントを接続できるようになります。 選択した個々のユーザーに対して有効になっている LinkedIn アカウント接続を持っていない場合は、何もする必要はありません。 選択した個々のユーザーに対する LinkedIn アカウント接続を以前に有効にした場合には、次のようにする必要があります。

1. 個々のユーザーの現在の一覧を取得する
1. 現在有効になっている個々のユーザーをグループに移行する
1. Azure AD 管理センターの LinkedIn アカウント接続設定で、選択したグループとして以前からのグループを使用します。

> [!NOTE]
> 現在選択されている個々のユーザーをグループに移行しない場合でも、Microsoft アプリで LinkedIn 情報を確認できます。

### <a name="move-currently-selected-users-to-a-group"></a>現在選択されているユーザーのグループへの移行

1. LinkedIn アカウント接続に選択されているユーザーの CSV ファイルを作成します。
1. 管理者アカウントで Microsoft 365 にサインインします。
1. PowerShell を起動します。
1. `Install-Module AzureAD` の実行による Azure AD モジュールのインストール
1. 次のスクリプトを実行します。

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

手順 2 からのグループを、Azure AD 管理センターの LinkedIn アカウント接続設定で選択したグループとして使用するには、「[Azure portal で LinkedIn アカウント接続を有効にする](#enable-linkedin-account-connections-in-the-azure-portal)」を参照してください。

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>グループ ポリシーを使用して LinkedIn アカウント接続を有効にする

1. [Office 2016 管理用テンプレート ファイル (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) をダウンロードします。
1. **ADMX** ファイルを抽出して中央のストアにコピーします。
1. [グループ ポリシーの管理] を開きます。
1. 次の設定を使用してグループ ポリシー オブジェクトを作成します。 **[ユーザーの構成]**  >  **[管理用テンプレート]**  >  **[Microsoft Office 2016]**  >  **[その他]**  >  **[LinkedIn の機能を Office アプリケーションで表示]** 。
1. **[有効]** または **[無効]** を選択します。
  
   State | 結果
   ------ | ------
   **有効** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が有効になります。 組織内のユーザーは、各自の Office 2016 アプリケーションで LinkedIn の機能を使用できます。
   **Disabled** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が無効になり、エンドユーザーはこの設定を変更できません。 組織内のユーザーがその Office 2016 アプリケーションで LinkedIn の機能を使用することはできません。

このグループ ポリシーが適用されるのは、ローカル コンピューターの Office 2016 アプリだけです。 ユーザーが各自の Office 2016 アプリで LinkedIn を無効にした場合でも、Microsoft 365 で LinkedIn の機能が表示されます。

## <a name="next-steps"></a>次のステップ

* [LinkedIn のユーザーの同意とデータ共有](linkedin-user-consent.md)

* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

* [Azure portal で現在の LinkedIn の統合設定を確認する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)