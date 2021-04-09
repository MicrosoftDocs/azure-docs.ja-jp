---
title: Azure AD Connect 管理エージェントとは - Azure AD Connect | Microsoft Docs
description: オンプレミス環境を Azure AD と同期させ、監視するために使用されるツールについて説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 09/04/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da4e1cfc68dff4ad2bc0552c6d35fe1230779306
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91312959"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Azure AD Connect 管理エージェントとは 
Azure AD Connect 管理エージェントは Azure Active Directory Connect の新しいコンポーネントであり、Azure Active Directory Connect サーバーにインストールできます。 サポート ケースを開いたとき、Microsoft のサポート エンジニアの問題解決に役立つ特定のデータを Active Directory 環境から収集するために使用されます。 

>[!NOTE]
>管理者エージェントはインストールされず、既定で有効になっています。  サポート ケースを支援するためにデータを収集するには、エージェントをインストールする必要があります。

インストールされると、Azure AD Connect 管理エージェントは Azure Active Directory からデータに対する特定の要求を待ち、同期環境から要求されたデータを取得し、Azure Active Directory にそのデータを送信します。Azure Active Directory でそのデータが Microsoft サポート エンジニアに提示されます。 

Azure AD Connect 管理エージェントが環境から取得した情報はどこにも格納されません。提出された Azure Active Directory Connect 関連のサポート ケースを調査し、問題を解決する目的で、Microsoft サポート エンジニアにのみ表示されます。Azure AD Connect 管理エージェントは既定では Azure AD Connect Server にはインストールされません。 

## <a name="install-the-azure-ad-connect-administration-agent-on-the-azure-ad-connect-server"></a>Azure AD Connect サーバーに Azure AD Connect 管理エージェントをインストールします。 

前提条件:
1.    Azure AD Connect がサーバーにインストールされている
2.    Azure AD Connect Health がサーバーにインストールされている

![管理エージェント](media/whatis-aadc-admin-agent/adminagent0.png)

Azure AD Connect 管理エージェントのバイナリは、AAD Connect サーバーに配置されます。 エージェントをインストールするには、次の操作を行います。

1.    管理者モードで powershell を開く
2.    「cd "C:\Program Files\Microsoft Azure Active Directory Connect\Tools"」で、アプリケーションが配置されているディレクトリに移動する
3.    ConfigureAdminAgent.ps1 を実行する

プロンプトが表示されたら、Azure AD グローバル管理者の資格情報を入力します。 これは Azure AD Connect のインストール時に入力した資格情報と同じである必要があります。

エージェントがインストールされると、サーバーのコントロール パネルの [プログラムの追加と削除] に次の新しいプログラムが 2 つ表示されます。 

![追加した新しいプログラムが含まれる [プログラムの追加と削除] リストのスクリーンショット。](media/whatis-aadc-admin-agent/adminagent1.png)

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Microsoft サービス エンジニアには自分の同期サービスのどのデータが表示されますか。 
サポート ケースを開くと、Microsoft サポート エンジニアは、特定のユーザーに関して、Active Directory の関連データ、Azure Active Directory Connect サーバーの Active Directory コネクター スペース、Azure Active Directory Connect サーバーのメタバースを見ることができます。 

Microsoft サポート エンジニアはシステム内のいかなるデータも変更できません。また、いかなるパスワードも表示できません。 

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Microsoft サポート エンジニアには自分のデータにアクセスして欲しくない場合、どうすればよいですか? 
エージェントがインストールされると、サポート時、Microsoft サービス エンジニアには自分のデータにアクセスして欲しくない場合、次の説明に従ってサービス構成ファイルを変更することで、機能を無効にできます。 

1. **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** をメモ帳で開きます。
2. **UserDataEnabled** 設定を下の画像のように無効にします。 **UserDataEnabled** 設定があり、true に設定されている場合、それを false に設定します。 この設定がない場合、次のように設定を追加します。    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3. 構成ファイルを保存します。
4. 下の画像のように Azure AD Connect 管理エージェント サービスを再起動します。

![Azure AD Administrator Agent サービスを再起動する場所を示すスクリーンショット。](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>次のステップ
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。
