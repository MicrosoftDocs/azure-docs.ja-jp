---
title: "Azure API Management でバックアップと復元を使用して障害復旧を実装する | Microsoft Docs"
description: "Azure API Management でバックアップと復元を使用して障害復旧を行う方法について説明します。"
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 6f10be3c-f796-4a6c-bacd-7931b6aa82af
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 1c812fc31011b57f2cdb357574877d6b7125280f
ms.openlocfilehash: c5ae5049588d5bc7628442942e71f182a425fead
ms.lasthandoff: 02/10/2017

---
# <a name="how-to-implement-disaster-recovery-using-service-backup-and-restore-in-azure-api-management"></a>Azure API Management でサービスのバックアップと復元を使用して障害復旧を実装する方法
Azure API Management を介して API の発行と管理を行うように選択することで、そうしなければ自分で設計、実装、および管理する必要のある、さまざまなフォールト トレランス機能やインフラストラクチャ機能を利用できるようになります。 Azure プラットフォームにより、わずかな料金で潜在的な障害の大部分が軽減されます。

API Management サービスがホストされているリージョンに影響する可用性の問題から復旧するためには、いつでも異なるリージョンにサービスを再構成できるように準備しておく必要があります。 可用性の目標と復旧時間の目標に応じて、バックアップ サービスを&1; つ以上のリージョンに確保し、それらの構成と内容がアクティブ サービスと同期するように保守することができます。 サービス バックアップと復元の機能は、障害復旧戦略を実装するために必要な構成要素となります。

このガイドでは、Azure リソース マネージャーの要求を認証する方法と、API Management サービス インスタンスをバックアップおよび復元する方法を説明します。

> [!NOTE]
> 障害復旧用に API Management サービス インスタンスをバックアップおよび復元するプロセスは、ステージングなどのシナリオ用に API Management サービス インスタンスをレプリケートするためにも使用できます。
>
> 各バックアップの有効期限は 30 日間です。 30 日の有効期限が切れた後にバックアップを復元しようとしても、" `Cannot restore: backup expired` " というメッセージが表示されて復元は失敗します。
>
>

## <a name="authenticating-azure-resource-manager-requests"></a>Azure リソース マネージャーの要求の認証
> [!IMPORTANT]
> バックアップと復元用の REST API では、Azure リソース マネージャーが使用されます。API Management のエンティティ管理には REST API 以外の認証メカニズムも用意されています。 このセクションの手順では、Azure リソース マネージャーの要求を認証する方法について説明します。 詳細については、「[Azure REST API Reference (Azure REST API リファレンス)](http://msdn.microsoft.com/library/azure/dn790557.aspx)」を参照してください。
>
>

Azure リソース マネージャーを使用してリソースに実行するすべてのタスクは、Azure Active Directory で以下の手順に従って認証する必要があります。

* Azure Active Directory テナントにアプリケーションを追加する。
* 追加したアプリケーションのアクセス許可を設定する。
* Azure リソース マネージャーへの要求を認証するためのトークンを取得する。

最初の手順は、Azure Active Directory アプリケーションの作成です。 API Management サービス インスタンスが含まれたサブスクリプションを使用して [Azure クラシック ポータル](http://manage.windowsazure.com/) にログインし、既定の Azure Active Directory の **[アプリケーション]** タブに移動します。

> [!NOTE]
> Azure Active Directory の既定のディレクトリがアカウントに表示されない場合は、必要なアクセス許可をアカウントに付与するよう Azure サブスクリプションの管理者に連絡してください。 既定のディレクトリを見つける方法の詳細については、「[Windows VM と共に使用する Azure Active Directory での職場または学校の ID を作成する](../virtual-machines/virtual-machines-windows-create-aad-work-id.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」の「Azure クラシック ポータルで既定のディレクトリを見つける」を参照してください。
>
>

![Azure Active Directory アプリケーションを作成する][api-management-add-aad-application]

**[追加]**、**[組織で開発中のアプリケーションを追加]** を順にクリックし、**[ネイティブ クライアント アプリケーション]** を選択します。 わかりやすい名前を入力し、次へ進む矢印をクリックします。 **[リダイレクト URI]** に`http://resources` などのプレース ホルダー URL を入力します。これは必須フィールドですが、値を後で使用することはありません。 チェック ボックスをオンにして、アプリケーションを保存します。

アプリケーションが保存されたら、**[構成]** をクリックし、ページを **[他のアプリケーションに対するアクセス許可]** セクションが表示されるまで下へスクロールして、**[アプリケーションの追加]** をクリックします。

![Add permissions][api-management-aad-permissions-add]

**[Windows** **Azure Service Management API]** を選択し、チェック ボックスをオンにしてアプリケーションを追加します。

![Add permissions][api-management-aad-permissions]

新たに追加された **Windows** **Azure Service Management API** アプリケーションの横にある **[デリゲートされたアクセス許可]** をクリックし、**[Azure Service 管理へのアクセス (プレビュー)]** のチェック ボックスをオンにして、**[保存]** をクリックします。

![Add permissions][api-management-aad-delegated-permissions]

サービス インスタンスをバックアップおよび復元する API を呼び出す前に、トークンを取得する必要があります。 次の例では、 [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet パッケージを使用して、トークンを取得します。

```c#
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace GetTokenResourceManagerRequests
{
    class Program
    {
        static void Main(string[] args)
        {
            var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenant id}");
            var result = authenticationContext.AcquireToken("https://management.azure.com/", {application id}, new Uri({redirect uri});

            if (result == null) {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }

            Console.WriteLine(result.AccessToken);

            Console.ReadLine();
        }
    }
}
```

`{tentand id}`、`{application id}`、および `{redirect uri}` を、次の指示に従って置き換えます。

`{tenant id}` を、作成した Azure Active Directory アプリケーションのテナント ID に置き換えます。 ID にアクセスするには、 **[エンドポイントの表示]**をクリックします。

![エンドポイント][api-management-aad-default-directory]

![エンドポイント][api-management-endpoint]

`{application id}` および `{redirect uri}` を、Azure Active Directory アプリケーションの **[構成]** タブにある **[リダイレクト URI]** セクションに表示されている**クライアント ID** と URL に置き換えます。

![リソース][api-management-aad-resources]

値を指定したら、次の例のようなコードによってトークンが返されます。

![トークン][api-management-arm-token]

以降のセクションで説明されているバックアップおよび復元の操作を呼び出す前に、REST 呼び出しに承認要求ヘッダーを設定します。

```c#
request.Headers.Add(HttpRequestHeader.Authorization, "Bearer " + token);
```

## <a name="step1"> </a>API Management サービスのバックアップ
API Management サービスをバックアップするには、次の HTTP 要求を発行します。

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/backup?api-version={api-version}`

各値の説明:

* `subscriptionId` - バックアップ対象の API Management サービスを含むサブスクリプションの ID
* `resourceGroupName` - "Api-Default-{service-region}" 形式の文字列。ここで、`service-region` はバックアップ対象の API Management サービスがホストされている Azure リージョンです。例: `North-Central-US`
* `serviceName` - バックアップを作成する API Management サービスの、作成時に指定された名前
* `api-version` - `2014-02-14` に置き換えます。

要求の本文に、ターゲットの Azure ストレージ アカウント名、アクセス キー、BLOB コンテナー名、バックアップ名を指定します。

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

`Content-Type` 要求ヘッダーの値を `application/json` に設定します。

バックアップは完了まで何分もかかる長時間の処理です。  要求が成功してバックアップ処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答状態コードを受け取ります。  `Location` ヘッダー内の URL に "GET" 要求を出して、処理のステータスを確認します。 バックアップの進行中は、「202 Accepted」状態コードの受け取りが続きます。 応答コードの `200 OK` は、バックアップ処理が正常に終了したことを示します。

バックアップ要求を行う際には、次の制約があることに注意してください。

* 要求の本文に指定された**コンテナー**は、**存在する必要があります**。
* バックアップの進行中には、SKU のアップグレードやダウングレード、ドメイン名の変更、その他の操作などの、 **サービス管理操作は試行しないでください** 。
* バックアップの復元は、作成されたときから **30 日間だけ保証されます**。
* 分析レポートの生成に使用される**使用状況データ**は、バックアップに**含まれません**。 [Azure API Management REST API][Azure API Management REST API] を使用して、分析レポートを保管用に定期的に取り出します。
* サービス バックアップを実行する頻度は、復旧ポイントの目標に影響を与えます。 その頻度を最小限に抑えるため、定期的なバックアップを実装すると共に、API Management サービスに対して重要な変更を行った後のオンデマンドのバックアップを実行することをお勧めします。
* バックアップ処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われる**変更**は、**バックアップに含まれないので失われる可能性があります**。

## <a name="step2"> </a>API Management サービスの復元
以前に作成されたバックアップから API Management サービスを復元するには、次の HTTP 要求を行います。

`POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/restore?api-version={api-version}`

各値の説明:

* `subscriptionId` - バックアップの復元先の API Management サービスを含むサブスクリプションの ID
* `resourceGroupName` - "Api-Default-{service-region}" 形式の文字列。ここで、`service-region` はバックアップの復元先の API Management サービスがホストされている Azure リージョンです。例: `North-Central-US`
* `serviceName` - 復元先の API Management サービスの、作成時に指定された名前
* `api-version` - `2014-02-14` に置き換えます。

要求の本文に、バックアップ ファイルの場所、つまり Azure ストレージ アカウント名、アクセス キー、BLOB コンテナー名、バックアップ名を指定します。

```
'{  
    storageAccount : {storage account name for the backup},  
    accessKey : {access key for the account},  
    containerName : {backup container name},  
    backupName : {backup blob name}  
}'
```

`Content-Type` 要求ヘッダーの値を `application/json` に設定します。

復元は、完了までに 30 分以上かかることのある長時間の操作です。  要求が成功して復元処理が開始されると、`Location` ヘッダーのある `202 Accepted` 応答状態コードを受け取ります。  `Location` ヘッダー内の URL に "GET" 要求を出して、処理のステータスを確認します。 復元の進行中は、「202 Accepted」状態コードの受け取りが続きます。 応答コードの `200 OK` は、復元処理が正常に終了したことを示します。

> [!IMPORTANT]
> 復元先のサービスの **SKU** は、復元されるバックアップ サービスの SKU と**一致しなければなりません**。
>
> 復元処理の進行中にサービス構成 (API、ポリシー、開発者ポータルの外観など) に対して行われる**変更**は、**上書きされることがあります**。
>
>

## <a name="next-steps"></a>次のステップ
バックアップ/復元プロセスに関する&2; つのチュートリアルを、次の Microsoft ブログで参照してください。

* [Azure API Management アカウントのレプリケート](https://www.returngis.net/en/2015/06/replicate-azure-api-management-accounts/)
  * Gisela さんによって投稿された記事です。
* [Azure API Management: 構成のバックアップと復元](http://blogs.msdn.com/b/stuartleeks/archive/2015/04/29/azure-api-management-backing-up-and-restoring-configuration.aspx)
  * Stuart さんによる詳細なアプローチで、公式のガイダンスに沿ったものではありませんが、非常に興味深い記事です。

[Backup an API Management service]: #step1
[Restore an API Management service]: #step2


[Azure API Management REST API]: http://msdn.microsoft.com/library/azure/dn781421.aspx

[api-management-add-aad-application]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-add-aad-application.png

[api-management-aad-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions.png
[api-management-aad-permissions-add]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-permissions-add.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-delegated-permissions.png
[api-management-aad-default-directory]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-default-directory.png
[api-management-aad-resources]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-aad-resources.png
[api-management-arm-token]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-arm-token.png
[api-management-endpoint]: ./media/api-management-howto-disaster-recovery-backup-restore/api-management-endpoint.png

