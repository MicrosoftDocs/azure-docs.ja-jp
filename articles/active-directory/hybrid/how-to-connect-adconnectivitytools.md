---
title: Azure AD Connect:ADConnectivityTool PowerShell モジュールとは | Microsoft Docs
description: このドキュメントでは、新しい ADConnectivity PowerShell モジュールとトラブルシューティングのための使用方法について説明します。
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 4/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7eed3e06ab42671d9674ad3893a88dfe9817e22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95973458"
---
# <a name="troubleshoot-azure-ad-connectivity-with-the-adconnectivitytool-powershell-module"></a>ADConnectivityTool PowerShell モジュールを使用した Azure AD 接続性のトラブルシューティング

ADConnectivity ツールは、次のいずれかの場合に使用される PowerShell モジュールです。

- インストール時、ネットワーク接続に問題があるために、ユーザーがウィザード上で入力した Active Directory 資格情報を検証できません。
- インストール後、ユーザーが PowerShell セッションから関数を呼び出した場合。

このツールの場所は **C:\Program Files\Microsoft Azure Active Directory Connect\Tools\ ADConnectivityTool.psm1** です 

## <a name="adconnectivitytool-during-installation"></a>インストール時の ADConnectivityTool

Azure AD Connect ウィザードの **[ディレクトリの接続]** ページにおいてネットワークの問題が発生した場合、ADConnectivityTool の関数の 1 つが自動的に使用されて、何が起きているかが判別されます。  次の問題は、いずれもネットワークの問題と考えられます。

- ユーザーが指定したフォレストの名前に入力ミスがあった、またはそのフォレストが存在しない 
- ユーザーが指定したフォレストに関連付けられているドメイン コントローラーの UDP ポート 389 が閉じられている
- [AD フォレスト アカウント] ウィンドウに入力された資格情報に、ターゲット フォレストに関連付けられているドメイン コントローラーを取得する権限がない
- ユーザーが指定したフォレストに関連付けられているドメイン コントローラーの TCP ポート 53、88、389 のいずれかが閉じられている 
- UDP 389 と (1 つ以上の) TCP ポートの両方が閉じられている
- 指定されたフォレスト、関連付けられたドメイン コントローラー、またはその両方について、DNS を解決できなかった

これらのいずれかの問題がある場合は、関連するエラー メッセージが AADConnect ウィザードに表示されます。


![エラー](media/how-to-connect-adconnectivitytools/error1.png)

たとえば、 **[ディレクトリの接続]** 画面でディレクトリを追加しようとしているとき、Azure AD Connect による確認が必要であり、ポート 389 経由で Azure AD Connect とドメイン コントローラーの通信が可能である必要があります。  それができない場合、上記のスクリーンショットに示されているエラーが表示されます。  

背後で実際に起きていることは、Azure AD Connect による `Start-NetworkConnectivityDiagnosisTools` 関数の呼び出しです。  この関数は、ネットワーク接続の問題が原因で資格情報の検証が失敗したときに呼び出されます。

最後に、ウィザードからツールが呼び出されるたびに詳細なログ ファイルが生成されます。 そのログの場所は **C:\ProgramData\AADConnect\ADConnectivityTool-\<date>-\<time>.log** です。

## <a name="adconnectivitytools-post-installation"></a>インストール後の ADConnectivityTools
Azure AD Connect がインストールされた後、ADConnectivityTools PowerShell モジュールのどの関数でも使用できます。  

関数のリファレンス情報は、[ADConnectivityTools リファレンス](reference-connect-adconnectivitytools.md)のページにあります。

### <a name="start-connectivityvalidation"></a>Start-ConnectivityValidation

この関数を呼び出す理由は、ADConnectivityTool.psm1 を PowerShell にインポートした後では、この関数は手動で **しか** 呼び出せないためです。 

この関数によって実行されるロジックは、指定された AD 資格情報を検証するために Azure AD Connect ウィザードによって実行されるロジックと同じです。  ただし、問題と推奨されている解決方法に関する、より詳細な説明が提供されます。 

接続の検証は、次の手順で構成されます。
-   ドメイン FQDN (完全修飾ドメイン名) オブジェクトを取得する
-   ユーザーが [新しい AD アカウントを作成] を選択した場合は、資格情報が Enterprise Administrators グループに属していることを検証する
-   フォレスト FQDN オブジェクトを取得する
-   先ほど取得したフォレスト FQDN オブジェクトに関連付けられた少なくとも 1 つのドメインにアクセスできることを確認する
-   フォレストの機能レベルが Windows Server 2003 以上であることを確認する

ユーザーは、これらすべてのアクションが正常に実行された場合に、ディレクトリを追加できるようになります。

ユーザーが問題の解決後にこの関数を実行した場合 (または問題がまったく存在しない場合)、Azure AD Connect ウィザードに戻って資格情報を再度入力することをユーザーに求める出力が表示されます。



## <a name="next-steps"></a>次の手順
- [Azure AD Connect:アカウントとアクセス許可](reference-connect-accounts-permissions.md)
- [高速インストール](how-to-connect-install-express.md)
- [カスタム インストール](how-to-connect-install-custom.md)
- [ADConnectivityTools リファレンス](reference-connect-adconnectivitytools.md)

