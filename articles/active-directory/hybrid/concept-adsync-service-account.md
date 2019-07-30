---
title: Azure AD Connect:ADSync サービス アカウント | Microsoft Docs
description: このトピックでは、ADSync サービス アカウントについて説明し、アカウントに関するベスト プラクティスを示します。
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/27/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f228da5afc5998d8fa59ce2d720cec4c9f955b67
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67479048"
---
# <a name="adsync-service-account"></a>ADSync サービス アカウント
Azure AD Connect は、Active Directory と Azure Active Directory の間の同期を調整するオンプレミス サービスをインストールします。  Microsoft Azure AD Sync 同期サービス (ADSync) は、オンプレミス環境内のサーバー上で実行されます。  サービスの資格情報は、高速インストールでは既定で設定されますが、組織のセキュリティ要件を満たすようにカスタマイズできます。  これらの資格情報は、オンプレミス フォレストまたは Azure Active Directory への接続には使用されません。

ADSync サービス アカウントの選択は重要な計画上の決定であり、Azure AD Connect をインストールする前に行います。  インストール後に資格情報を変更しようとすると、サービスの開始に失敗し、同期データベースへのアクセスが失われ、接続先ディレクトリ (Azure および AD DS) の認証は失敗します。  元の資格情報が復元されるまで、同期は行われません。

## <a name="the-default-adsync-service-account"></a>既定の ADSync サービス アカウント

メンバー サーバー上で実行するとき、AdSync サービスは仮想サービス アカウント (VSA) のコンテキストで実行されます。  製品の制限により、ドメイン コントローラーへのインストール時はカスタム サービス アカウントが作成されます。  簡単設定のサービス アカウントが組織のセキュリティ要件を満たしていない場合は、[カスタマイズ] オプションを選択して Azure AD Connect をデプロイします。  次に、組織の要件を満たしているサービス アカウント オプションを選択します。

>[!NOTE]
>ドメイン コントローラーへのインストール時の既定のサービス アカウントは Domain\AAD_InstallationIdentifier の形式です。  このアカウントのパスワードはランダムに生成され、復旧とパスワード ローテーションに関して大きな課題に直面します。  Microsoft では、ドメイン コントローラーへの初期インストール中に、スタンドアロンまたはグループの管理されたサービス アカウント (sMSA/gMSA) を使用するようにサービス アカウントをカスタマイズすることをお勧めします。

|Azure AD Connect の場所|作成されたサービス アカウント|
|-----|-----|
|メンバー サーバー|NT SERVICE\ADSync|
|ドメイン コントローラー|Domain\AAD_74dc30c01e80 (注を参照)|

## <a name="custom-adsync-service-accounts"></a>カスタム ADSync サービス アカウント
Microsoft では、仮想サービス アカウントか、スタンドアロンまたはグループの管理されたサービス アカウントのコンテキストで ADSync サービスを実行することをお勧めします。  特定組織のセキュリティ要件を満たすようにプロビジョニングされたサービス アカウントをドメイン管理者が作成することもできます。   インストール中に使用されるサービス アカウントをカスタマイズするには、下の [簡単設定] ページで [カスタマイズ] オプションを選択します。   次のオプションを使用できます。

- 既定のアカウント - Azure AD Connect は前述のようにサービス アカウントをプロビジョニングします
- 管理されたサービス アカウント - 管理者によってプロビジョニングされたスタンドアロンまたはグループの MSA を使用します
- ドメイン アカウント - 管理者によってプロビジョニングされたドメイン サービス アカウントを使用します

![](media/concept-adsync-service-account/adsync1.png)

![](media/concept-adsync-service-account/adsync2.png)

## <a name="diagnosing-adsync-service-account-changes"></a>ADSync サービス アカウントの変更の診断
インストール後に ADSync サービスの資格情報を変更すると、サービスの開始に失敗し、同期データベースへのアクセスが失われ、接続先ディレクトリ (Azure および AD DS) の認証は失敗します。  新しい ADSync サービス アカウントへのデータベース アクセスを許可するだけではこの問題から復旧できません。 元の資格情報が復元されるまで、同期は行われません。

ADSync サービスが開始できない場合、サービスはエラー レベルのメッセージをイベント ログに書き込みます。  メッセージの内容は、組み込みデータベース (localdb) とフル SQL のどちらが使用されているかによって異なります。  書き込まれる可能性があるイベント ログ エントリの例を次に示します。

### <a name="example-1"></a>例 1

AdSync サービスの暗号化キーが見つからないため、再作成されました。  この問題が修正されるまで同期は行われません。

この問題のトラブルシューティング: AdSync サービスの [ログオン] 資格情報が変更されると、Microsoft Azure AD Sync の暗号化キーにアクセスできなくなります。  資格情報が変更されている場合は、[サービス] アプリケーションを使用して [ログオン] アカウントを元々構成されていた値 (例: NT SERVICE\AdSync) に再変更してサービスを再起動します。  これで、AdSync サービスの正常動作がすぐに復元されます。

詳細については、次の[記事](https://go.microsoft.com/fwlink/?linkid=2086764)を参照してください。

### <a name="example-2"></a>例 2

ローカル データベース (localdb) への接続を確立できなかったため、サービスを開始できませんでした。

この問題のトラブルシューティング: AdSync サービスの [ログオン] 資格情報が変更されると、Microsoft Azure AD Sync サービスは、ローカル データベース プロバイダーへのアクセス許可を失います。  資格情報が変更されている場合は、[サービス] アプリケーションを使用して [ログオン] アカウントを元々構成されていた値 (例: NT SERVICE\AdSync) に再変更してサービスを再起動します。  これで、AdSync サービスの正常動作がすぐに復元されます。

詳細については、次の[記事](https://go.microsoft.com/fwlink/?linkid=2086764)を参照してください。

追加の詳細: プロバイダーから次のエラー情報が返されました。
 

``` 
OriginalError=0x80004005 OLEDB Provider error(s): 
Description  = 'Login timeout expired'
Failure Code = 0x80004005
Minor Number = 0 
Description  = 'A network-related or instance-specific error has occurred while establishing a connection to SQL Server. Server is not found or not accessible. Check if instance name is correct and if SQL Server is configured to allow remote connections. For more information see SQL Server Books Online.'
```
## <a name="next-steps"></a>次の手順
「 [オンプレミス ID と Azure Active Directory の統合](whatis-hybrid-identity.md)」をご覧ください。