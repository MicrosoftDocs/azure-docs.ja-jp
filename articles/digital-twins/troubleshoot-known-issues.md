---
title: 既知の問題 - Azure Digital Twins
description: Azure Digital Twinds の既知の問題を認識し、軽減するのに役立ちます。
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 01d962db45a58781ca5f2ba494de16ad420b0807
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921071"
---
# <a name="known-issues-in-azure-digital-twins"></a>Azure Digital Twins の既知の問題

この記事では、Azure Digital Twins に関連する既知の問題について説明します。

## <a name="400-client-error-bad-request-in-cloud-shell"></a>Cloud Shell の "400 Client Error:Bad Request"

Cloud Shell のコマンドが断続的に失敗して、エラー "400 Client Error:Bad Request for url: http://localhost:50342/oauth2/token" を受け取り、その後に完全なスタック トレースが示されることがあります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

これは、`az login` コマンドを再実行し、その後のログイン手順を完了することによって解決できます。

この後、コマンドを再実行できるようになります。

### <a name="possible-causes"></a>考えられる原因

これは Cloud Shell の既知の問題の結果です。"[*Cloud Shell からのトークンの取得が断続的に失敗し、400 Client Error:Bad Request を受け取る*](https://github.com/Azure/azure-cli/issues/11749)"

## <a name="missing-role-assignment-after-scripted-setup"></a>スクリプトを使用したセットアップ後にロールの割り当てが存在しない

ユーザーによっては、[*方法: インスタンスと認証の設定 (スクリプト化) に関する記事のロールの割り当て部分で問題が発生することがあります。インスタンスと認証の設定 (スクリプト化)* ](how-to-set-up-instance-scripted.md) に関するページを参照してください。 このスクリプトでは失敗として示されませんが、*Azure Digital Twins 所有者 (プレビュー)* ロールがユーザーに正常に割り当てられておらず、これは、今後その他のリソースを作成する機能に影響します。

スクリプトの実行後にロールの割り当てが正常に設定されたかどうかを確認するには、セットアップの記事にある「[*ユーザー ロールの割り当てを確認する*](how-to-set-up-instance-scripted.md#verify-user-role-assignment)」の手順に従ってください。 ユーザーに対してこのロールが表示されていない場合、この問題による影響があります。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

これを解決するには、CLI または Azure portal を使用して、手動でロールの割り当てを設定します。 

以下の手順に従います。
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>考えられる原因

個人の [Microsoft アカウント (MSA)](https://account.microsoft.com/account) でログインしているユーザーの場合、このようなコマンドにおいてユーザーの識別に使用されるユーザーのプリンシパル ID が、ユーザーのログイン メールとは異なる場合があります。そのため、スクリプトによってこれを検出し、使用してロールを適切に割り当てることが困難になります。

## <a name="issue-with-interactive-browser-authentication"></a>対話型ブラウザーの認証に関する問題

**[Azure.Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) ライブラリ**のバージョン **1.2.0** を使用して Azure Digital Twins アプリケーションで認証コードを記述すると、[InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) メソッドで問題が発生することがあります。

これはライブラリの最新バージョンではありません。 最新バージョンは **1.2.2** です。

影響を受けるメソッドは、次の記事で使用されています。 
* [*チュートリアル:クライアント アプリをコーディングする*](tutorial-code.md)
* [*方法: アプリ認証コードを作成する*](how-to-authenticate-client.md)
* [*方法: Azure Digital Twins の API および SDK を使用する*](how-to-use-apis-sdks.md)

この問題には、ブラウザー ウィンドウで認証を試みた際に表示される "Azure.Identity.AuthenticationFailedException" というエラー応答が含まれます。 ブラウザー ウィンドウが完全に起動できなくなるか、一見ユーザーが正常に認証されたかのように見せてクライアント アプリケーションがエラーで失敗します。

### <a name="troubleshooting-steps"></a>トラブルシューティングの手順

解決するには、Azure.Identity バージョン **1.2.2** を使用するようにアプリケーションを更新します。 このバージョンのライブラリを使用することで、ブラウザーが想定どおりに読み込まれ、認証されるはずです。

### <a name="possible-causes"></a>考えられる原因

これは、最新版 Azure.Identity ライブラリ (バージョン **1.2.0**) の未解決問題に関連しています。[*InteractiveBrowserCredential の使用時に認証に失敗します*](https://github.com/Azure/azure-sdk-for-net/issues/13940)。

この問題は、Azure Digital Twins アプリケーションでバージョン **1.2.0** を使用している場合や、バージョンを指定せずにライブラリをプロジェクトに追加した場合 (これによっても最新バージョンが規定で使用されます) に発生します。

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のセキュリティとアクセス許可の詳細を確認します。
* [*概念:Azure Digital Twins ソリューションのセキュリティ*](concepts-security.md)