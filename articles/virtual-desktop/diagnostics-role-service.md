---
title: Windows Virtual Desktop の診断の問題 - Azure
description: Windows Virtual Desktop の診断機能を使用して問題を診断する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 09/21/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70676bd1a07acdfcbba071a906b390ed66d70074
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91279860"
---
# <a name="identify-and-diagnose-windows-virtual-desktop-issues"></a>Windows Virtual Desktop 問題を特定し、診断する

>[!IMPORTANT]
>このコンテンツは、Azure Resource Manager Windows Virtual Desktop オブジェクトを含む Windows Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを使用しない Windows Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)を参照してください。

Windows Virtual Desktop では、管理者が単一のインターフェイスを使用して問題を特定できる診断機能が提供されます。 Windows Virtual Desktop の診断機能の詳細については、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics.md)」を参照してください。

診断ロール サービス自体が Windows Virtual Desktop の一部であるため、Windows Virtual Desktop に到達しない接続は診断結果に表示されません。 Windows Virtual Desktop 接続の問題は、エンドユーザーにネットワーク接続の問題が発生しているときに発生する可能性があります。

## <a name="common-error-scenarios"></a>一般的なエラー シナリオ

WVDErrors テーブルでは、あらゆる種類のアクティビティを対象にエラーが追跡記録されます。 "ServiceError" という名前の列には、"True" または "False" という印の付いた追加フラグがあります。 このフラグにより、エラーがサービスに関連しているのかどうかがわかります。

* 値が "True" の場合、サービス チームはこの問題を既に調査している可能性があります。 これがユーザー エクスペリエンスに影響を与え、高い頻度で現れる場合、Windows Virtual Desktop のサポート チケットを送信することをお勧めします。
* 値が "False" の場合、構成に誤りがある可能性があり、自分で修正できます。 エラー メッセージを見れば、どこから始めたら良いかヒントが与えられます。

次の表では、管理者が経験する可能性のある一般的なエラーの一覧を示します。

>[!NOTE]
>この一覧には、最も一般的なエラーが含まれており、定期的に更新されます。 最新の情報を得るために、必ず 1 か月に 1 回以上この記事をご確認ください。

## <a name="management-errors"></a>管理エラー

|エラー メッセージ|推奨されている解決方法|
|---|---|
|登録キーを作成できませんでした |登録トークンを作成できませんでした。 有効期限を短くして (1 時間から 1 か月)、もう一度作成してみてください。 |
|登録キーを削除できませんでした|登録トークンを削除できませんでした。 もう一度削除してみてください。 それでもうまくいかない場合は、PowerShell を使用して、トークンがまだ存在するかどうかを確認します。 存在する場合は、PowerShell を使用して削除します。|
|セッション ホストのドレイン モードを変更できませんでした |VM でドレイン モードを変更できませんでした。 VM の状態を確認します。 VM を使用できない場合、ドレイン モードを変更することはできません。|
|ユーザー セッションを切断できませんでした |VM からユーザーを切断できませんでした。 VM の状態を確認します。 VM を使用できない場合、ユーザー セッションを切断することはできません。 VM を使用できる場合は、ユーザー セッションの状態を確認して、接続が切断されていないかどうかを確認します。 |
|セッション ホスト内のすべてのユーザーをログオフできませんでした |VM からユーザーをサインアウトすることができませんでした。 VM の状態を確認します。 使用できない場合、ユーザーはサインアウトできません。ユーザー セッションの状態を確認して、既にサインアウトされていないかどうかを確認します。PowerShell を使用して強制的にサインアウトすることができます。 |
|アプリケーション グループからユーザーの割り当てを解除できませんでした|ユーザーのアプリ グループの公開を取り消すことができませんでした。 ユーザーが Azure AD で利用できるかどうかを確認します。 ユーザーが、アプリ グループが公開されているユーザー グループに属しているかどうかを確認します。 |
|利用できる場所の取得でエラーが発生しました |ホスト プールの作成ウィザードで使用されている VM の場所を確認します。 その場所でイメージを使用できない場合は、その場所にイメージを追加するか、別の VM の場所を選択します。 |

### <a name="connection-error-codes"></a>接続エラー コード

|数値コード|エラー コード|推奨されている解決方法|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|セッション ホストは、Active Directory に正しく参加していません。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|セッション ホストが使用できないため、接続が失敗しました。 セッション ホストの正常性を確認してください。|
|-2146233088|ConnectionFailedClientDisconnect|このエラーが頻繁に発生する場合は、ユーザーのコンピューターがネットワークに接続されていることを確認してください。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|ホスト ユーザーが接続しようとしたセッションが正常ではありません。 仮想マシンをデバッグしてください。|
|-2146233088|ConnectionFailedUserNotAuthorized|ユーザーは、公開されたアプリまたはデスクトップにアクセスするためのアクセス許可を持っていません。 このエラーは、公開されたリソースを管理者が削除した後に発生する可能性があります。 リモート デスクトップ アプリケーション内のフィードを更新するようユーザーに依頼してください。|
|2|FileNotFound|ユーザーがアクセスしようとしたアプリケーションが正しくインストールされていないか、正しくないパスに設定されています。<br><br>ユーザーがアクティブなセッションを使用しているときに新しいアプリを公開すると、ユーザーはこのアプリにアクセスできなくなります。 ユーザーがアプリにアクセスできるようにするには、セッションをシャットダウンして再起動する必要があります。 |
|3|InvalidCredentials|ユーザーが入力したユーザー名またはパスワードが、既存のどのユーザー名またはパスワードとも一致しません。 資格情報に入力ミスがないことを確認し、もう一度やり直してください。|
|8|ConnectionBroken|クライアントとゲートウェイまたはサーバーとの間の接続が削除されました。 予期せず発生した場合を除き、アクションは不要です。|
|14|UnexpectedNetworkDisconnect|ネットワークへの接続が削除されました。 ユーザーにもう一度接続するよう依頼してください。|
|24|ReverseConnectFailed|ホスト仮想マシンには、RD ゲートウェイへの直接の見通し線がありません。 ゲートウェイ IP アドレスを解決できることを確認してください。|

## <a name="error-cant-add-user-assignments-to-an-app-group"></a>エラー:アプリ グループにユーザー割り当てを追加できない

アプリ グループにユーザーを割り当てた後に、"セッションが終了している" または "認証の問題が発生している (拡張機能 Microsoft_Azure_WVD)" ことを示す警告が Azure portal に表示されます。 そして、割り当てページは読み込まれず、その後、Azure portal 全体 (Azure Monitor、Log Analytics、Service Health など) でページの読み込みが停止します。

**原因:** 条件付きアクセス ポリシーに問題があります。 Azure portal は、SharePoint Online に依存する Microsoft Graph のトークンを取得しようとしています。 お客様には、データ ストレージにアクセスするための使用条件に同意することをユーザーに求める "Microsoft Office 365 Data Storage Terms of Use" という条件付きアクセス ポリシーがあります。 しかし、まだサインインしていないため、Azure portal はトークンを取得できません。

**解決策:** Azure portal にサインインする前に、まず、管理者が SharePoint にサインインして使用条件に同意する必要があります。 その後、通常のように Azure portal にサインインできるようになります。

## <a name="next-steps"></a>次のステップ

Windows Virtual Desktop 内のロールについて詳しくは、「[Windows Virtual Desktop 環境](environment-setup.md)」をご覧ください。

Windows Virtual Desktop に使用可能な PowerShell コマンドレットの一覧を表示するには、[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)に関する記事をご覧ください。
