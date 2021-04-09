---
title: Azure Media Services v3 API に接続する - Python
description: この記事では、Python を使用して Media Services v3 API に接続する方法を示します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: devx-track-python
ms.openlocfilehash: 76df8baaf170b05762b93478a496eb1e9ed802d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94916748"
---
# <a name="connect-to-media-services-v3-api---python"></a>Media Services v3 API に接続する - Python

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、サービス プリンシパルによるサインイン方式を使用して Azure Media Services v3 Python SDK に接続する方法を説明します。

## <a name="prerequisites"></a>前提条件

- [python.org](https://www.python.org/downloads/) から Python をダウンロードします
- `PATH` 環境変数を必ず設定します
- [Media Services アカウントを作成する](./create-account-howto.md) リソース グループ名と Media Services アカウント名を覚えておいてください。
- [API へのアクセス](./access-api-howto.md)に関するトピックの手順を実行します。 後の手順で必要になるので、サブスクリプション ID、アプリケーション ID (クライアント ID)、認証キー (シークレット)、テナント ID を控えておきます。

> [!IMPORTANT]
> [命名規則](media-services-apis-overview.md#naming-conventions)を確認してください。

## <a name="install-the-modules"></a>モジュールをインストールする

Python を使用して Azure Media Services を操作するには、以下のモジュールをインストールする必要があります。

* `azure-mgmt-resource` モジュール。これには、Active Directory 用の Azure モジュールが含まれています。
* `azure-mgmt-media` モジュール。これには、Media Services エンティティが含まれています。

    [Media Services SDK for Python の最新バージョン](https://pypi.org/project/azure-mgmt-media/)を取得していることを確認してください。

コマンド ライン ツールを開き、以下のコマンドを使用してモジュールをインストールします。

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==3.0.0
```

## <a name="connect-to-the-python-client"></a>Python クライアントに接続する

1. `.py` という拡張子でファイルを作成します
1. 使い慣れたエディターでファイルを開きます
1. 次のコードをファイルに追加します。 このコードによって、必要なモジュールがインポートされ、Media Services への接続に必要な Active Directory 資格情報オブジェクトが作成されます。

      変数の値を、[API へのアクセス](./access-api-howto.md)の手順で取得した値に設定します

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. ファイルを実行します

## <a name="next-steps"></a>次のステップ

- [Python SDK](https://aka.ms/ams-v3-python-sdk) を使用します。
- Media Services の [Python リファレンス](/python/api/overview/azure/mediaservices/management) ドキュメントを確認してください。
