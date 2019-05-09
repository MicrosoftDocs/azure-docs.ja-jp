---
title: Azure AD Connect 管理エージェントとは - Azure AD Connect | Microsoft Docs
description: オンプレミス環境を Azure AD と同期させ、監視するために使用されるツールについて説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/03/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33143626e136523b4af086e841b92e9ad30fa86
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/15/2019
ms.locfileid: "59577611"
---
# <a name="what-is-the-azure-ad-connect-admin-agent"></a>Azure AD Connect 管理エージェントとは 
Azure AD Connect 管理エージェントは Azure Active Directory Connect の新しいコンポーネントであり、Azure Active Directory Connect サーバーにインストールされます。 サポート ケースを開いたとき、Microsoft のサポート エンジニアの問題解決に役立つ特定のデータを Active Directory 環境から収集するために使用されます。

インストールされると、Azure AD Connect 管理エージェントは Azure Active Directory からデータに対する特定の要求を待ち、同期環境から要求されたデータを取得し、Azure Active Directory にそのデータを送信します。Azure Active Directory でそのデータが Microsoft サポート エンジニアに提示されます。

Azure AD Connect 管理エージェントが環境から取得した情報はどこにも格納されません。提出された Azure Active Directory Connect 関連のサポート ケースを調査し、問題を解決する目的で、Microsoft サポート エンジニアにのみ表示されます。

## <a name="how-is-the-azure-ad-connect-admin-agent-installed-on-the-azure-ad-connect-server"></a>Azure AD Connect 管理エージェントはどのように Azure AD Connect サーバーにインストールされますか。 
管理エージェントがインストールされると、サーバーのコントロール パネルの [プログラムの追加と削除] に新しいプログラムが 2 つ表示されます。 

![管理エージェント](media/whatis-aadc-admin-agent/adminagent1.png)

Azure AD Connect インストールの非常に重要な部分であるため、これらのプログラムは削除したり、アンインストールしたりしないでください。

## <a name="what-data-in-my-sync-service-is-shown-to-the-microsoft-service-engineer"></a>Microsoft サービス エンジニアには自分の同期サービスのどのデータが表示されますか。
サポート ケースを開くと、Microsoft サポート エンジニアは、特定のユーザーに関して、Active Directory の関連データ、Azure Active Directory Connect サーバーの Active Directory コネクター スペース、Azure Active Directory Connect サーバーのメタバースを見ることができます。

Microsoft サポート エンジニアはシステム内のいかなるデータも変更できません。また、いかなるパスワードも表示できません。

## <a name="what-if-i-dont-want-the-microsoft-support-engineer-to-access-my-data"></a>Microsoft サポート エンジニアには自分のデータにアクセスして欲しくない場合、どうすればよいですか。 
 
サポート時、Microsoft サービス エンジニアには自分のデータにアクセスして欲しくない場合、次の説明に従ってサービス構成ファイルを変更することで、これを無効にできます。 

1.  **C:\Program Files\Microsoft Azure AD Connect Administration Agent\AzureADConnectAdministrationAgentService.exe.config** をメモ帳で開きます。
2.  **UserDataEnabled** 設定を下の画像のように無効にします。 **UserDataEnabled** 設定があり、true に設定されている場合、それを false に設定します。 この設定がない場合、次のように設定を追加します。    

    ```xml
    <appSettings>
      <add key="TraceFilename" value="ADAdministrationAgent.log" />
      <add key="UserDataEnabled" value="false" />
    </appSettings>
    ```

3.  構成ファイルを保存します。
4.  下の画像のように Azure AD Connect 管理エージェント サービスを再起動します。

![管理エージェント](media/whatis-aadc-admin-agent/adminagent2.png)

## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。