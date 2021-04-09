---
title: Connect Health を使用した Azure AD での AD FS サインイン | Microsoft Docs
description: このドキュメントでは、AD FS サインインを Azure AD Connect Health サインイン レポートに統合する方法について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74769feba1d717a2f1a72d311f85bdfbeac7b7db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574779"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>Connect Health を使用した Azure AD での AD FS サインイン - プレビュー

AD FS サインインは、Connect Health を使用して Azure Active Directory サインイン レポートに統合できるようになりました。 [Azure AD サインイン レポート](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-all-sign-ins#:~:text=Interactive%20user%20sign-ins%20are%20sign-ins%20where%20a%20user,to%20Azure%20AD%20or%20to%20a%20helper%20app.)には、ユーザー、アプリケーション、マネージド リソースがいつ Azure AD にサインインしてリソースにアクセスしたかに関する情報が含まれています。 

Connect Health for AD FS エージェントでは、要求とエラーの詳細 (要求が失敗した場合) に関する情報を提供するために、サーバーのバージョンに依存する AD FS の複数のイベント ID を関連付けます。 これらの情報は Azure AD サインイン レポート スキーマに関連付けられ、Azure AD サインイン レポート UX に表示されます。 このレポートと共に、新しい Log Analytics ストリームが AD FS データや新しい Azure Monitor ブック テンプレートで使用できます。 このテンプレートは、AD FS アカウントのロックアウト、不適切なパスワードの試行、予期しないサインイン試行のスパイクなどのシナリオに関する詳細な分析のために使用したり、変更したりできます。

## <a name="prerequisites"></a>前提条件
* インストールされて最新バージョンにアップグレードされた Azure AD Connect Health for AD FS。
* Azure AD サインインを表示するためのグローバル管理者またはレポート閲覧者ロール

## <a name="what-data-is-displayed-in-the-report"></a>レポートに表示されるデータ
使用可能なデータは、Azure AD サインインに使用できるものと同じデータを反映します。Azure AD または AD FS のいずれかのサインインの種類に基づいて、情報が記載された 5 つのタブが使用できます。 Connect Health では、サーバーのバージョンに依存する AD FS のイベントを関連付け、それらを AD FS スキーマと照合します。 



#### <a name="user-sign-ins"></a>ユーザー サインイン 
サインイン ブレードの各タブには、次の既定値が表示されます。
* サインイン日
* 要求 ID
* ユーザー名またはユーザー ID
* サインインの状態
* サインインに使用されるデバイスの IP アドレス
* サインイン識別子

#### <a name="authentication-method-information"></a>認証方法の情報
認証タブには、次の値が表示される場合があります。認証方法は、AD FS の監査ログから取得されます。

|認証方法|説明|
|-----|-----|
|フォーム|ユーザー名/パスワード認証|
|Windows|Windows 統合認証|
|Certificate|スマート カードまたは VirtualSmart 証明書による認証|
|WindowsHelloForBusiness|このフィールドは、Windows Hello for Business での認証のためのものです。 (Microsoft Passport 認証)|
|Device | デバイス認証がイントラネットまたはエクストラネットからの "プライマリ" 認証として選択されており、デバイス認証が実行された場合に表示されます。  このシナリオでは、別のユーザー認証はありません。| 
|フェデレーション|AD FS では認証は行われませんでしたが、それがサードパーティの ID プロバイダーに送信されました|
|SSO |シングル サインオン トークンが使用された場合は、このフィールドが表示されます。 SSO に MFA が含まれている場合は、多要素として表示されます|
|多要素|シングル サインオン トークンに MFA が含まれており、それが認証に使用された場合、このフィールドは多要素として表示されます|
|Azure MFA|Azure MFA が AD FS の追加の認証プロバイダーとして選択されており、認証に使用されました|
|ADFSExternalAuthenticationProvider|このフィールドは、サードパーティの認証プロバイダーが登録され、認証に使用された場合です|


#### <a name="ad-fs-additional-details"></a>AD FS の追加の詳細
AD FS サインインでは、次の詳細が使用できます。
* サーバー名
* IP チェーン
* Protocol

### <a name="enabling-log-analytics-and-azure-monitor"></a>Log Analytics と Azure Monitor の有効化
Log Analytics は AD FS サインインに対して有効にすることができ、他の任意の Log Analytics 統合コンポーネント (Sentinel など) で使用できます。

> [!NOTE] 
> AD FS サインインを使用すると、組織内での AD FS へのサインインの量によっては、Log Analytics のコストが大幅に増える可能性があります。 Log Analytics を有効および無効にするには、ストリームのチェックボックスを選択してください。

この機能で Log Analytics を有効にするには、[Log Analytics] ブレードに移動し、[ADFSSignIns] ストリームを選択します。 この選択によって、AD FS サインインが Log Analytics に流入できるようになります。

更新された Azure Monitor ブック テンプレートにアクセスするには、[Azure Monitor テンプレート] に移動し、[サインイン] ブックを選択します。
ブックの詳細については、「[Azure Monitor ブック](https://aka.ms/adfssigninspreview)」を参照してください。




### <a name="frequently-asked-questions"></a>よく寄せられる質問
***表示される可能性があるサインインの種類は何ですか?***
サインイン レポートでは、O-Auth、WS-Fed、SAML、WS-Trust の各プロトコルを使用したサインインがサポートされています。 

***各種のサインインはサインイン レポートにどのように表示されますか?***
シームレス SSO サインインが実行される場合は、1 つの関連付け ID を含むサインインの 1 行が表示されます。
単一要素認証が実行される場合は、2 行に同じ関連付け ID が表示されますが、2 つの異なる認証方法 (つまり、フォーム、SSO) が含まれます。
多要素認証の場合は、共有されている関連付け ID と 3 つの対応する認証方法 (つまり、フォーム、AzureMFA、多要素) を含む 3 行が表示されます。 この特定の例では、多要素はこの場合、SSO に MFA が含まれていることを示しています。

***レポートに表示される可能性があるエラーはどのようなものですか?***
サインイン レポートや説明に表示される AD FS に関連したエラーの完全な一覧については、[AD FS ヘルプのエラー コード リファレンス](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)を参照してください

***サインインの "ユーザー" セクションに "00000000-0000-0000-0000-000000000000" が表示されます。これはどういう意味ですか。***
サインインが失敗し、試行された UPN が既存の UPN に一致しない場合、[ユーザー]、[ユーザー名]、[ユーザー ID] の各フィールドは "00000000-0000-0000-0000-000000000000" になり、[サインイン識別子] にはユーザーが入力した試行された値が入力されます。 これらの場合、サインインしようとしているユーザーは存在しません。

***オンプレミスのイベントを Azure AD サインイン レポートに関連付けるにはどうすればよいですか?***
AD FS 用の Azure AD Connect Health エージェントでは、サーバーのバージョンに依存する AD FS のイベント ID を関連付けます。 これらのイベントは、AD FS サーバーのセキュリティ ログで使用可能になります。 

***一部の AD FS サインインの [アプリケーション ID] や [アプリケーション名] に NotSet または NotApplicable が表示されるのはなぜですか?***
AD FS サインイン レポートには、OAuth のサインイン用の [アプリケーション ID] フィールドに OAuth ID が表示されます。WS-Fed の WS-Trust サインイン シナリオでは、アプリケーション ID は NotSet または NotApplicable になり、リソース ID と証明書利用者 ID が [リソース ID] フィールドに表示されます。

***プレビュー段階のレポートには他にも何か既知の問題がありますか?***
このレポートには、[基本情報] タブの [認証要件] フィールドに、サインインには関係なく、AD FS サインインに対して単一要素認証の値が入力されるという既知の問題があります。 さらに、[認証の詳細] タブの [要件] フィールドには [プライマリまたはセカンダリ] が表示されますが、[プライマリ] または [セカンダリ] の認証の種類を区別するための修正作業が進行中です。


## <a name="related-links"></a>関連リンク
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health エージェントのインストール](how-to-connect-health-agent-install.md)
* [危険な IP レポート](how-to-connect-health-adfs-risky-ip.md)





