---
title: Azure AD Connect 同期:Azure AD Connect 同期サービス アカウントの変更 | Microsoft Docs
description: このトピックでは、暗号化キーの詳細と、パスワードの変更後にこのキーを破棄する方法について説明します。
services: active-directory
keywords: Azure AD 同期サービス アカウント, パスワード
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 76b19162-8b16-4960-9e22-bd64e6675ecc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15d0d537a23e21eeda3b284e7ec706cde2b443e7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58014085"
---
# <a name="changing-the-azure-ad-connect-sync-service-account-password"></a>Azure AD Connect 同期サービス アカウントのパスワードの変更
Azure AD Connect 同期サービス アカウントのパスワードを変更すると、暗号化キーを破棄し、Azure AD Connect 同期サービス アカウントのパスワードを再初期化するまで、同期サービスを正常に開始できなくなります。 

Azure AD Connect は同期サービスの一部として、暗号化キーを使用して AD DS と Azure AD サービスの各アカウントのパスワードを保存します。  これらのアカウントは、データベースへの保存前に暗号化されます。 

暗号化キーは、[Windows データ保護 (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) を使用して保護されています。 DPAPI では、**Azure AD Connect 同期サービス アカウントのパスワード**を使用して暗号化キーを保護します。 

サービス アカウントのパスワードを変更する必要がある場合は、「[Azure AD Connect 同期の暗号化キーの破棄](#abandoning-the-azure-ad-connect-sync-encryption-key)」の手順に従って変更します。  この手順は、なんらかの理由で暗号化キーを破棄する必要がある場合にも使用してください。

## <a name="issues-that-arise-from-changing-the-password"></a>パスワードの変更により生じる問題
サービス アカウントのパスワードを変更する場合、2 つの手順を完了する必要があります。

最初に、Windows サービス コントロール マネージャーでパスワードを変更する必要があります。  この問題を解決するまで、次のエラーが表示されます。


- Windows サービス コントロール マネージャーで同期サービスを開始しようとすると、"**ローカル コンピューターの Microsoft Azure AD Sync サービスを開始できませんでした。** **エラー 1069:ログオンに失敗したため、サービスを開始できませんでした**" というエラーが表示されます。
- Windows イベント ビューアーでは、システム イベント ログに**イベント ID 7038** のエラーと、"**現在構成されているパスワードでは、次のエラーにより ADSync サービスにログオンできませんでした:ユーザー名またはパスワードが正しくありません**" というメッセージが記録されます。

次に、特定の条件下では、パスワードを更新すると同期サービスで DPAPI を使用して暗号化キーを取得できなくなります。 暗号化キーがないと、同期サービスはオンプレミスの AD および Azure AD と同期するために必要なパスワードの暗号化を解除できません。
次のようなエラーが表示されます。

- Windows サービス コントロール マネージャーで同期サービスを開始しようとすると、暗号化キーを取得できないため、"<strong>ローカル コンピューターで Microsoft Azure AD Sync サービスを開始できませんでした。詳細情報はシステム イベント ログを参照してください。これが Microsoft 以外のサービスである場合は、サービス ベンダーに問い合わせてください。その際、サービス固有のエラー コードが -21451857952 であることを伝えてください</strong>" というエラーが表示され失敗します。
- Windows イベント ビューアーでは、アプリケーション イベント ログに**イベント ID 6028** のエラーと、*"The server encryption key cannot be accessed."* (サーバー暗号化キーにアクセスできませんでした) というエラー メッセージが記録されます。

これらのエラーが表示されないようにするために、パスワードの変更時には「[Azure AD Connect 同期の暗号化キーの破棄](#abandoning-the-azure-ad-connect-sync-encryption-key)」の手順に従ってください。
 
## <a name="abandoning-the-azure-ad-connect-sync-encryption-key"></a>Azure AD Connect 同期の暗号化キーの破棄
>[!IMPORTANT]
>次の手順は、ビルド 1.1.443.0 以前の Azure AD Connect にのみ適用されます。

暗号化キーを破棄するには、次の手順を実行します。

### <a name="what-to-do-if-you-need-to-abandon-the-encryption-key"></a>暗号化キーを破棄する必要がある場合の対処方法

暗号化キーを破棄する必要がある場合は、次の手順に従って破棄を行います。

1. [同期サービスを停止する](#stop-the-synchronization-service)

1. [既存の暗号化キーを破棄する](#abandon-the-existing-encryption-key)

2. [AD DS アカウントのパスワードを入力する](#provide-the-password-of-the-ad-ds-account)

3. [Azure AD 同期アカウントのパスワードを再初期化する](#reinitialize-the-password-of-the-azure-ad-sync-account)

4. [同期サービスを開始する](#start-the-synchronization-service)

#### <a name="stop-the-synchronization-service"></a>同期サービスを停止する
まず、Windows サービス コントロール マネージャーでサービスを停止できます。  サービスを停止する場合は、そのサービスが実行されていないことを確認してください。  サービスが実行されている場合は、完了するまで待ってから停止します。


1. Windows サービス コントロール マネージャーにアクセスします ([スタート]、[サービス] の順に移動します)。
2. **[Microsoft Azure AD Sync] \(Microsoft Azure AD 同期)** を選択して [停止] をクリックします。

#### <a name="abandon-the-existing-encryption-key"></a>既存の暗号化キーを破棄する
新しい暗号化キーを作成できるように、既存の暗号化キーを破棄します。

1. 管理者として Azure AD Connect サーバーにログインします。

2. 新しい PowerShell セッションを開始します。

3. `$env:Program Files\Microsoft Azure AD Sync\bin\` フォルダーに移動します。

4. `./miiskmu.exe /a` コマンドを実行します

![Azure AD Connect 同期の暗号化キー破棄ユーティリティ](./media/how-to-connect-sync-change-serviceacct-pass/key5.png)

#### <a name="provide-the-password-of-the-ad-ds-account"></a>AD DS アカウントのパスワードを入力する
データベース内に保存されている既存のパスワードの暗号化を解除できなくなるため、同期サービスに AD DS アカウントのパスワードを入力する必要があります。 同期サービスでは、新しい暗号化キーを使用してこのパスワードを暗号化します。

1. Synchronization Service Manager を起動します ([スタート]、[同期サービス] の順に移動します)。
</br>![Sync Service Manager](./media/how-to-connect-sync-change-serviceacct-pass/startmenu.png)  
2. **[コネクタ]** タブに移動します。
3. オンプレミス AD に対応する **AD コネクタ** を選択します。 AD コネクタが複数ある場合は、各コネクタについて次の手順を繰り返します。
4. **[アクション]** の **[プロパティ]** を選択します。
5. ポップアップ ダイアログで、**[Connect to Active Directory Forest] \(Active Directory フォレストに接続)** を選択します。
6. AD DS アカウントのパスワードを **[パスワード]** テキストボックスに入力します。 パスワードがわからない場合は、この手順を実行する前に既知の値に設定する必要があります。
7. **[OK]** をクリックして新しいパスワードを保存し、ポップアップ ダイアログを閉じます。
![Azure AD Connect 同期の暗号化キー破棄ユーティリティ](./media/how-to-connect-sync-change-serviceacct-pass/key6.png)

#### <a name="reinitialize-the-password-of-the-azure-ad-sync-account"></a>Azure AD 同期アカウントのパスワードを再初期化する
同期サービスに Azure AD サービス アカウントのパスワードを直接指定することはできません。 代わりに、**Add-ADSyncAADServiceAccount** コマンドレットを使用して、Azure AD サービス アカウントを再初期化する必要があります。 このコマンドレットによりアカウントのパスワードがリセットされ、同期サービスで利用できるようになります。

1. Azure AD Connect サーバーで新しい PowerShell セッションを開始します。
2. `Add-ADSyncAADServiceAccount` コマンドレットを実行します。
3. ポップアップ ダイアログで、Azure AD テナントの Azure AD グローバル管理者の資格情報を入力します。
![Azure AD Connect 同期の暗号化キー破棄ユーティリティ](./media/how-to-connect-sync-change-serviceacct-pass/key7.png)
4. 成功した場合は、PowerShell コマンド プロンプトが表示されます。

#### <a name="start-the-synchronization-service"></a>同期サービスを開始する
これで、同期サービスで必要な暗号化キーとすべてのパスワードにアクセスできるようになったので、Windows サービス コントロール マネージャーでサービスを再起動します。


1. Windows サービス コントロール マネージャーにアクセスします ([スタート]、[サービス] の順に移動します)。
2. **[Microsoft Azure AD Sync] \(Microsoft Azure AD 同期)** を選択して [再起動] をクリックします。

## <a name="next-steps"></a>次の手順
**概要トピック**

* [Azure AD Connect 同期:同期を理解してカスタマイズする](how-to-connect-sync-whatis.md)

* [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)
