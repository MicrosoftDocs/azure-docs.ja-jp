---
title: Azure HDInsight での認証の問題
description: Azure HDInsight での認証の問題
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/24/2020
ms.openlocfilehash: b5a55b3b5f9affcd9f34e1c0d4c439a7ada8c0b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99095126"
---
# <a name="authentication-issues-in-azure-hdinsight"></a>Azure HDInsight での認証の問題

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

Azure Data Lake (Gen1 または Gen2) の管理対象であるセキュリティで保護されたクラスターでは、ドメイン ユーザーが HDI Gateway を介してクラスター サービスにサインインすると (Apache Ambari ポータルへのサインインなど)、HDI Gateway では、まず Azure Active Directory (Azure AD) から OAuth トークンの取得、次に Azure AD DS から Kerberos チケットの取得が試行されます。 これらのいずれかの段階で認証に失敗する可能性があります。 この記事の目的は、これらの問題の一部をデバッグすることです。

認証に失敗すると、資格情報の入力を求められます。 このダイアログをキャンセルすると、エラー メッセージが出力されます。 一般的なエラー メッセージの一部を次に示します。

## <a name="invalid_grant-or-unauthorized_client-50126"></a>invalid_grant または unauthorized_client、50126

### <a name="issue"></a>問題

フェデレーション ユーザーのサインインがエラー コード 50126 で失敗します (クラウド ユーザーのサインインは成功します)。 エラー メッセージは次のようになります。

```
Reason: Bad Request, Detailed Response: {"error":"invalid_grant","error_description":"AADSTS70002: Error validating credentials. AADSTS50126: Invalid username or password\r\nTrace ID: 09cc9b95-4354-46b7-91f1-efd92665ae00\r\n Correlation ID: 4209bedf-f195-4486-b486-95a15b70fbe4\r\nTimestamp: 2019-01-28 17:49:58Z","error_codes":[70002,50126], "timestamp":"2019-01-28 17:49:58Z","trace_id":"09cc9b95-4354-46b7-91f1-efd92665ae00","correlation_id":"4209bedf-f195-4486-b486-95a15b70fbe4"}
```

### <a name="cause"></a>原因

Azure AD エラー コード 50126 は、`AllowCloudPasswordValidation` ポリシーがテナントによって設定されていないことを意味します。

### <a name="resolution"></a>解決策

Azure AD テナントの全体管理者は、ADFS の管理対象ユーザーのパスワード ハッシュを Azure AD で使用できるようにする必要があります。  記事「`AllowCloudPasswordValidationPolicy`HDInsight で Enterprise セキュリティ パッケージを使用する[」で示されているように、](../domain-joined/apache-domain-joined-architecture.md) を適用します。

---

## <a name="invalid_grant-or-unauthorized_client-50034"></a>invalid_grant または unauthorized_client、50034

### <a name="issue"></a>問題

エラー コード 50034 でサインインが失敗します。 エラー メッセージは次のようになります。

```
{"error":"invalid_grant","error_description":"AADSTS50034: The user account Microsoft.AzureAD.Telemetry.Diagnostics.PII does not exist in the 0c349e3f-1ac3-4610-8599-9db831cbaf62 directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: bbb819b2-4c6f-4745-854d-0b72006d6800\r\nCorrelation ID: b009c737-ee52-43b2-83fd-706061a72b41\r\nTimestamp: 2019-04-29 15:52:16Z", "error_codes":[50034],"timestamp":"2019-04-29 15:52:16Z","trace_id":"bbb819b2-4c6f-4745-854d-0b72006d6800", "correlation_id":"b009c737-ee52-43b2-83fd-706061a72b41"}
```

### <a name="cause"></a>原因

ユーザー名が正しくありません (存在しません)。 ユーザーは、Azure portal で使用されているものと同じユーザー名を使用していません。

### <a name="resolution"></a>解決策

そのポータルで使用できるものと同じユーザー名を使用します。

---

## <a name="invalid_grant-or-unauthorized_client-50053"></a>invalid_grant または unauthorized_client、50053

### <a name="issue"></a>問題

ユーザー アカウントがロックアウトされています。エラー コードは 50053 です。 エラー メッセージは次のようになります。

```
{"error":"unauthorized_client","error_description":"AADSTS50053: You've tried to sign in too many times with an incorrect user ID or password.\r\nTrace ID: 844ac5d8-8160-4dee-90ce-6d8c9443d400\r\nCorrelation ID: 23fe8867-0e8f-4e56-8764-0cdc7c61c325\r\nTimestamp: 2019-06-06 09:47:23Z","error_codes":[50053],"timestamp":"2019-06-06 09:47:23Z","trace_id":"844ac5d8-8160-4dee-90ce-6d8c9443d400","correlation_id":"23fe8867-0e8f-4e56-8764-0cdc7c61c325"}
```

### <a name="cause"></a>原因

正しくないパスワードを使用して何度もサインインが試行されました。

### <a name="resolution"></a>解決策

30 分ほど待つか、認証しようとしているアプリケーションがあれば終了します。

---

## <a name="invalid_grant-or-unauthorized_client-50053-2"></a>invalid_grant または unauthorized_client、50053 (#2)

### <a name="issue"></a>問題

パスワードの有効期限が切れました。エラー コードは 50053 です。 エラー メッセージは次のようになります。

```
{"error":"user_password_expired","error_description":"AADSTS50055: Password is expired.\r\nTrace ID: 241a7a47-e59f-42d8-9263-fbb7c1d51e00\r\nCorrelation ID: c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a\r\nTimestamp: 2019-06-06 17:29:37Z","error_codes":[50055],"timestamp":"2019-06-06 17:29:37Z","trace_id":"241a7a47-e59f-42d8-9263-fbb7c1d51e00","correlation_id":"c7fe4a42-67e4-4acd-9fb6-f4fb6db76d6a","suberror":"user_password_expired","password_change_url":"https://portal.microsoftonline.com/ChangePassword.aspx"}
```

### <a name="cause"></a>原因

パスワードの有効期限が切れています。

### <a name="resolution"></a>解決策

(オンプレミス システムの) Azure portal でパスワードを変更し、同期が完了するまで 30 分間待ちます。

---

## <a name="interaction_required"></a>interaction_required

### <a name="issue"></a>問題

エラー メッセージ `interaction_required` を受信します。

### <a name="cause"></a>原因

条件付きアクセス ポリシーまたは MFA がユーザーに適用されています。 対話型認証はまだサポートされていないため、ユーザーまたはクラスターを MFA/条件付きアクセスから除外する必要があります。 クラスターを除外することを選択した場合は (IP アドレス ベースの除外ポリシー)、その vnet に対して AD `ServiceEndpoints` が有効になっていることを確認します。

### <a name="resolution"></a>解決策

「[Azure Active Directory Domain Services を使用して、Enterprise セキュリティ パッケージで HDInsight クラスターを構成する](./apache-domain-joined-configure-using-azure-adds.md)」に示されているように、条件付きアクセス ポリシーを使用して MFA から HDInisght クラスターを除外します。

---

## <a name="sign-in-denied"></a>サインインが拒否される

### <a name="issue"></a>問題

サインインが拒否されます。

### <a name="cause"></a>原因

この段階に到達するには、OAuth 認証は問題ではありませんが、Kerberos 認証は問題です。 このクラスターが ADLS の管理対象である場合、Kerberos 認証が試行される前に OAuth サインインは正常に完了しています。 WASB クラスター上では、OAuth のサインインは試行されません。 Kerberos エラーにはさまざまな理由が考えられます。たとえば、パスワード ハッシュが同期されていない、ユーザー アカウントが Azure AD DS でロックアウトされているなどです。 パスワード ハッシュは、ユーザーがパスワードを変更した場合にのみ同期されます。 Azure AD DS インスタンスを作成すると、作成後に変更されたパスワードの同期が開始されます。 開始前に設定されたパスワードがさかのぼって同期されることはありません。

### <a name="resolution"></a>解決策

パスワードが同期されていないと思われる場合は、パスワードを変更して、同期されるまで数分間待ちます。

SSH 接続を試します。ドメインに参加しているマシンから、同じユーザーの資格情報を使用して認証 (kinit) を試行する必要があります。 ローカル ユーザーを使用してヘッド/エッジ ノードに SSH で接続し、kinit を実行します。

---

## <a name="kinit-fails"></a>kinit が失敗する

### <a name="issue"></a>問題

kinit が失敗します。

### <a name="cause"></a>原因

状況に応じて異なります。

### <a name="resolution"></a>解決策

kinit が成功するには、自分の `sAMAccountName` を把握しておく必要があります (これは、領域のない短いアカウント名です)。 通常、`sAMAccountName` はアカウントのプレフィックスです (`bob@contoso.com` の BLOB など)。 ユーザーによって異なる場合があります。 自分の `sAMAccountName` を知るには、ディレクトリを参照または検索できる必要があります。

`sAMAccountName` を確認する方法:

* ローカルの Ambari 管理者を使用して Ambari にサインインできる場合は、ユーザーの一覧を確認します。

* [ドメインに参加している Windows コンピューター](../../active-directory-domain-services/tutorial-create-management-vm.md)がある場合は、標準の Windows AD ツールを使用して参照できます。 これには、ドメイン内に有効なアカウントが必要です。

* ヘッド ノードから、SAMBA コマンドを使用して検索することができます。 これには、有効な Kerberos セッション (kinit の成功) が必要です。 net ads search "(userPrincipalName=bob*)"

    検索または参照の結果には、`sAMAccountName` 属性が表示されます。 また、`pwdLastSet`、`badPasswordTime`、`userPrincipalName` などの他の属性を調べて、それらのプロパティが期待どおりに一致するかどうかを確認することもできます。

---

## <a name="kinit-fails-with-preauthentication-failure"></a>kinit が Preauthentication エラーで失敗する

### <a name="issue"></a>問題

kinit が `Preauthentication` エラーで失敗します。

### <a name="cause"></a>原因

ユーザー名またはパスワードが正しくありません。

### <a name="resolution"></a>解決策

ユーザー名とパスワードを確認します。 また、前述した他のプロパティも確認します。 詳細なデバッグを有効にするには、kinit を試す前に、セッションから `export KRB5_TRACE=/tmp/krb.log` を実行します。

---

## <a name="job--hdfs-command-fails-due-to-tokennotfoundexception"></a>TokenNotFoundException が原因でジョブまたは HDFS コマンドが失敗する

### <a name="issue"></a>問題

`TokenNotFoundException` が原因でジョブまたは HDFS コマンドが失敗します。

### <a name="cause"></a>原因

ジョブまたはコマンドが成功するために必要な OAuth アクセス トークンが見つかりませんでした。 ADLS または ABFS ドライバーでは、ストレージ要求を行う前に、資格情報サービスから OAuth アクセス トークンの取得が試行されます。 このトークンは、同じユーザーを使用して Ambari ポータルにサインインするときに登録されます。

### <a name="resolution"></a>解決策

ジョブの実行に ID が使用されているユーザー名で、Ambari ポータルへのログインが 1 回成功することを確認します。

---

## <a name="error-fetching-access-token"></a>アクセス トークンのフェッチ エラー

### <a name="issue"></a>問題

ユーザーがエラー メッセージ `Error fetching access token` を受け取ります。

### <a name="cause"></a>原因

このエラーは、ユーザーが ACL を使用して ADLS Gen2 にアクセスしようとしたときに、Kerberos トークンの有効期限が切れていると、断続的に発生します。

### <a name="resolution"></a>解決策

* Azure Data Lake Storage Gen1 の場合は、ブラウザー キャッシュを消去し、もう一度 Ambari にログインします。

* Azure Data Lake Storage Gen2 の場合は、ユーザーがログインしようとしているユーザーの `/usr/lib/hdinsight-common/scripts/RegisterKerbTicketAndOAuth.sh <upn>` を実行します。

---

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]