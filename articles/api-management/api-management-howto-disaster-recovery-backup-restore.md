---
title: Azure API Management でバックアップと復元を使用してディザスター リカバリーを実装する | Microsoft Docs
description: Azure API Management でバックアップと復元を使用してディザスター リカバリーを行う方法について説明します。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 6507c39faecfa0e56fc19597e414e9d25d368567
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460872"
---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management でサービスのバックアップと復元を使用してディザスター リカバリーを実装する方法

Azure API Management を介して API の発行と管理を行うことで、そうしなければ手動で設計、実装、および管理する必要のある、フォールト トレランス機能やインフラストラクチャ機能を利用できるようになります。 Azure プラットフォームにより、わずかな料金で潜在的な障害の大部分が軽減されます。

API Management サービスをホストしているリージョンに影響する可用性の問題から復旧するためには、いつも別のリージョンにサービスを再構成できるように準備しておく必要があります。 目標復旧時間に応じて、1 つまたは複数のリージョンでスタンバイ サービスを保持する必要がある場合があります。 また、目標復旧時間に従って、アクティブ サービスと同期されているそれらの構成と内容の維持を試みる場合があります。 サービスのバックアップと復元の機能では、ディザスター リカバリー戦略を実装するために必要な構成要素が提供されます。

また、バックアップおよび復元操作は、開発やステージングなど、運用環境間の API Management サービス構成のレプリケートで使用することができます。 ユーザーやサブスクリプションなどのランタイム データも同様にコピーされ、望ましくない場合もあることに注意してください。

このガイドでは、バックアップおよび復元操作を自動化する方法と、Azure Resource Manager を使用して、確実にバックアップおよび復元要求の認証を成功させる方法を示します。

> [!IMPORTANT]
> 復元操作では、ターゲット サービスのカスタム ホスト名の構成は変更されません。 復元操作の完了後に、シンプルな DNS CNAME の変更で、トラフィックをスタンバイ インスタンスにリダイレクトできるように、アクティブとスタンバイの両方のサービスに対して同じカスタム ホスト名と TLS 証明書を使用することをおすすめします。
>
> バックアップ操作では、Azure portal の Analytics ブレードに表示されるレポートで使用される、事前に集計されたログ データはキャプチャされません。

> [!WARNING]
> 各バックアップの有効期限は 30 日間です。 30 日の有効期限が切れた後にバックアップを復元しようとしても、"`Cannot restore: backup expired`" というメッセージが表示されて復元は失敗します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="authenticating-azure-resource-manager-requests"></a>Azure Resource Manager の要求の認証

> [!IMPORTANT]
> バックアップと復元用の REST API では、Azure Resource Manager が使用されます。API Management のエンティティ管理には REST API 以外の認証メカニズムも用意されています。 このセクションの手順では、Azure Resource Manager の要求を認証する方法について説明します。 詳細については、「[Azure REST API Reference (Azure REST API リファレンス)](/rest/api/index)」を参照してください。

Azure Resource Manager を使用してリソースに実行するすべてのタスクは、Azure Active Directory で以下の手順に従って認証する必要があります。

-   Azure Active Directory テナントにアプリケーションを追加する。
-   追加したアプリケーションのアクセス許可を設定する。
-   Azure Resource Manager への要求を認証するためのトークンを取得する。

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. API Management サービス インスタンスを含むサブスクリプションを使用して、**Azure Active Directory** の **[アプリの登録]** タブ (Azure Active Directory > [登録の管理/アプリの登録]) に移動します。

    > [!NOTE]
    > Azure Active Directory の既定のディレクトリがアカウントに表示されない場合は、必要なアクセス許可をアカウントに付与するよう Azure サブスクリプションの管理者に連絡してください。

3. **[新しいアプリケーションの登録]** をクリックします。

    **[作成]** ウィンドウが右側に表示されます。 ここに AAD アプリの関連情報を入力します。

4. アプリケーションの名前を入力します。
5. アプリケーションの種類で **[ネイティブ]** を選択します。
6. **[リダイレクト URI]** に`http://resources` などのプレース ホルダー URL を入力します。これは必須フィールドですが、値を後で使用することはありません。 チェック ボックスをオンにして、アプリケーションを保存します。
7. **Create** をクリックしてください。

### <a name="add-an-application"></a>アプリケーションを追加する

1. アプリケーションが作成されたら、 **[設定]** をクリックします。
2. **[必要なアクセス許可]** をクリックします。
3. **[+ 追加]** をクリックします。
4. **[API を選択します]** を選択します。
5. **[Windows** **Azure Service Management API]** を選択します。
6. **[選択]** を選択します。

    ![Add permissions](./media/api-management-howto-disaster-recovery-backup-restore/add-app.png)

7. 新しく追加されたアプリケーションの横にある **[委任されたアクセス許可]** をクリックし、 **[Azure Service 管理へのアクセス (プレビュー)]** のチェック ボックスをオンにします。
8. **[選択]** を選択します。
9. **[アクセス許可の付与]** をクリックします。

### <a name="configuring-your-app"></a>アプリの構成

サービス インスタンスをバックアップおよび復元する API を呼び出す前に、トークンを取得する必要があります。 次の例では、[Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet パッケージを使用して、トークンを取得します。

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.microsoftonline.com/{tenant id}");
            var result = authenticationContext.AcquireTokenAsync("https://management.azure.com/", "{application id}", new Uri("{redirect uri}"), new PlatformParameters(PromptBehavior.Auto)).Result;

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

`{tenant id}`、`{application id}`、および `{redirect uri}` を、次の指示に従って置き換えます。

1. `{tenant id}` を、作成した Azure Active Directory アプリケーションのテナント ID に置き換えます。 ID にアクセスするには、 **[アプリの登録]**  ->  **[エンドポイント]** をクリックします。

    ![エンドポイント][api-management-endpoint]

2. **[設定]** ページに移動して、`{application id}` を取得した値に置き換えます。
3. `{redirect uri}`を Azure Active Directory アプリケーションの **[リダイレクト URI]** タブの値に置き換えます。

    値を指定したら、次の例のようなコードによってトークンが返されます。

    ![トークン][api-management-arm-token]

    > [!NOTE]
    > トークンは、一定期間後に失効することがあります。 新しいトークンを生成するには、コード サンプルを再度実行します。

## <a name="calling-the-backup-and-restore-operations"></a>バックアップおよび復元操作の呼び出し

REST API は [API Management Service - Backup](/rest/api/apimanagement/2019-01-01/apimanagementservice/backup) と [API Management Service - Restore](/rest/api/apimanagement/2019-01-01/apimanagementservice/restore) です。

以降のセクションで説明されている "バックアップおよび復元" の操作を呼び出す前に、REST 呼び出しに承認要求ヘッダーを設定します。

```csharp
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

### <a name="step1"> </a>API Management サービスのバックアップ

API Management サービスをバックアップするには、次の HTTP 要求を発行します。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}
```

各値の説明:

-   `subscriptionId` - バックアップ対象の API Management サービスを保持するサブスクリプションの ID
-   `resourceGroupName` - Azure API Management サービスのリソース グループの名前
-   `serviceName` - バックアップを作成する API Management サービスの、作成時に指定された名前
-   `api-version` - `2018-06-01-preview` に置き換えます

要求の本文に、ターゲットの Azure ストレージ アカウント名、アクセス キー、BLOB コンテナー名、バックアップ名を指定します。

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

`Content-Type` 要求ヘッダーの値を `application/json` に設定します。

バックアップは完了までに 1 分以上かかることもある、長時間の処理です。 要求が成功してバックアップ処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答状態コードを受け取ります。 `Location` ヘッダー内の URL に "GET" 要求を出して、処理のステータスを確認します。 バックアップの進行中は、"202 Accepted" 状態コードの受け取りが続きます。 応答コードの `200 OK` は、バックアップ処理が正常に終了したことを示します。

バックアップ要求を行う際には、次の制約があることに注意してください。

-   要求の本文に指定された**コンテナー**は、**存在する必要があります**。
-   バックアップの進行中は、**サービス管理に変更を加えることは避けてください** (SKU のアップグレードやダウングレード、ドメイン名の変更など)。
-   バックアップの復元は、作成されたときから **30 日間だけ保証されます**。
-   分析レポートの生成に使用される**使用状況データ**は、バックアップに**含まれません**。 [Azure API Management REST API][azure api management rest api] を使用して、分析レポートを保管用に定期的に取り出します。
-   サービス バックアップを実行する頻度は、復旧ポイントの目標に影響を与えます。 その頻度を最小限に抑えるため、定期的なバックアップを実装すると共に、API Management サービスに対して変更を行った後のオンデマンドのバックアップを実行することをお勧めします。
-   バックアップ処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われた**変更**は、**バックアップ対象から除外され、その結果失われる可能性があります**。

### <a name="step2"> </a>API Management サービスの復元

以前に作成されたバックアップから API Management サービスを復元するには、次の HTTP 要求を行います。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}
```

各値の説明:

-   `subscriptionId` - バックアップの復元先の API Management サービスを保持するサブスクリプションの ID
-   `resourceGroupName` - バックアップの復元先の Azure API Management サービスを保持するリソース グループの名前
-   `serviceName` - 復元先の API Management サービスの、作成時に指定された名前
-   `api-version` - `2018-06-01-preview` に置き換えます

要求の本文には、バックアップ ファイルの場所を指定します。 つまり、Azure ストレージ アカウント名、アクセス キー、BLOB コンテナー名、バックアップ名を追加します。

```json
{
    "storageAccount": "{storage account name for the backup}",
    "accessKey": "{access key for the account}",
    "containerName": "{backup container name}",
    "backupName": "{backup blob name}"
}
```

`Content-Type` 要求ヘッダーの値を `application/json` に設定します。

復元は、完了までに 30 分以上かかることのある長時間の操作です。 要求が成功して復元処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答状態コードを受け取ります。 `Location` ヘッダー内の URL に "GET" 要求を出して、処理のステータスを確認します。 復元の進行中は、"202 Accepted" 状態コードの受け取りが続きます。 応答コードの `200 OK` は、復元処理が正常に終了したことを示します。

> [!IMPORTANT]
> 復元先のサービスの **SKU** は、復元されるバックアップ サービスの SKU と**一致しなければなりません**。
>
> 復元処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われる**変更**は、**上書きされることがあります**。

<!-- Dummy comment added to suppress markdown lint warning -->

> [!NOTE]
> バックアップと復元の操作は、PowerShell の _Backup-AzApiManagement_ コマンドと _Restore-AzApiManagement_ コマンドでも実行できます。

## <a name="next-steps"></a>次の手順

次のリソースでバックアップ/復元プロセスの異なるチュートリアルを確認します。

-   [Azure API Management アカウントのレプリケート](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
-   [Logic Apps を使用した API Management Backup および Restore の自動化](https://github.com/Azure/api-management-samples/tree/master/tutorials/automating-apim-backup-restore-with-logic-apps)
-   [Azure API Management:構成のバックアップと復元](https://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
    _Stuart さんによる詳細なアプローチで、公式のガイダンスに沿ったものではありませんが、興味深い記事です。_

[backup an api management service]: #step1
[restore an api management service]: #step2
[azure api management rest api]: https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest
[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png
[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png
