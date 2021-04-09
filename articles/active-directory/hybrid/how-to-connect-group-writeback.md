---
title: Azure AD Connect:グループの書き戻し
description: この記事では、Azure AD Connect のグループの書き戻しについて説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 06/11/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c506c87ad5901754175f18e6b50bc6ed46a3c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98246912"
---
# <a name="azure-ad-connect-group-writeback"></a>Azure AD Connect のグループの書き戻し

グループの書き戻しを使用すると、お客様はハイブリッドのニーズに合わせてクラウド グループを活用できます。 Microsoft 365 グループ機能を使用している場合は、そのグループをオンプレミスの Active Directory 内に表示できます。 このオプションが使用できるのは、オンプレミスの Active Directory 内に Exchange が存在する場合 **のみ** です。

## <a name="pre-requisites"></a>前提条件
グループの書き戻しを有効にするには、次の前提条件を満たす必要があります。
- お使いのテナントの Azure Active Directory Premium ライセンス。
- オンプレミスの Exchange 組織と Microsoft 365 の間にハイブリッド デプロイが構成されており、正常に機能していることが確認されている。
- サポートされているバージョンの Exchange がオンプレミスにインストールされている
- Azure Active Directory Connect を使用したシングル サインオンが構成されている 

## <a name="enable-group-writeback"></a>グループの書き戻しを有効にする
グループの書き戻しを有効にするには、次の手順を使用します。

1. Azure AD Connect ウィザードを開き、 **[構成]** を選択し、 **[次へ]** をクリックします。
2. **[同期オプションのカスタマイズ]** を選択し、 **[次へ]** をクリックします。
3. **[Azure AD に接続]** ページで、資格情報を入力します。 **[次へ]** をクリックします。
4. **[オプション機能]** ページで、以前に構成したオプションが選択されたままであることを確認します。
5. **[グループの書き戻し]** を選択し、 **[次へ]** をクリックします。
6. **[書き戻し] ページ** Microsoft 365 からオンプレミスの組織に同期されるオブジェクトを格納する Active Directory 組織単位 (OU) を選択し、 **[次へ]** をクリックします。
7. **[構成の準備完了]** ページで、 **[構成]** をクリックします。
8. ウィザードが完了したら、[構成が完了しました] ページで **[終了]** をクリックします。
9. Azure Active Directory Connect サーバーで管理者として Windows PowerShell を開き、次のコマンドを実行します。

```Powershell
$AzureADConnectSWritebackAccountDN =  <MSOL_ account DN>
Import-Module "C:\Program Files\Microsoft Azure Active Directory Connect\AdSyncConfig\AdSyncConfig.psm1"

# To grant the <MSOL_account> permission to all domains in the forest:
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN

# To grant the <MSOL_account> permission to specific OU (eg. the OU chosen to writeback Office 365 Groups to):
$GroupWritebackOU = <DN of OU where groups are to be written back to>
Set-ADSyncUnifiedGroupWritebackPermissions -ADConnectorAccountDN $AzureADConnectSWritebackAccountDN -ADObjectDN $GroupWritebackOU
```

Microsoft 365 グループの構成の詳細については、「[オンプレミスの Exchange ハイブリッドを使用して Microsoft 365 グループを構成する](/exchange/hybrid-deployment/set-up-microsoft-365-groups#enable-group-writeback-in-azure-ad-connect)」を参照してください。

## <a name="disabling-group-writeback"></a>グループの書き戻しを無効にする
グループの書き戻しを無効にするには、次の手順を使用します。 


1. Azure Active Directory Connect ウィザードを起動し、[追加のタスク] ページに移動します。 **[同期オプションのカスタマイズ]** タスクを選択し、 **[次へ]** をクリックします。
2. **[オプション機能]** ページで、グループの書き戻しのチェックボックスをオフにします。  グループが削除されることを知らせる警告が表示されます。  **[はい]** をクリックします。
   >[!IMPORTANT]
   > グループの書き戻しを無効にすると、この機能によって以前に作成されたグループが、次回の同期サイクルでローカルの Active Directory から削除されます。 

   ![チェックボックスをオフにする](media/how-to-connect-group-writeback/group2.png)
  
3. **[次へ]** をクリックします。
4. **[構成]** をクリックします。

 >[!NOTE]
 > グループの書き戻しを無効にすると、Azure Active Directory コネクタでフル インポートおよび完全同期のフラグが 'true' に設定されます。これにより、次回の同期サイクルで規則の変更が反映され、以前に Active Directory に書き戻されたグループが削除されます。

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
