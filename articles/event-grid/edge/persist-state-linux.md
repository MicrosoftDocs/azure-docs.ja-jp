---
title: Linux で状態を永続化する - Azure Event Grid IoT Edge | Microsoft Docs
description: Linux でメタデータを永続化します
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171484"
---
# <a name="persist-state-in-linux"></a>Linux で状態を永続化する

Event Grid モジュールで作成したトピックとサブスクリプションは、既定でコンテナー ファイル システムに格納されます。 作成したメタデータが永続化されていない場合、モジュールを再デプロイするとすべて失われます。 データを複数のデプロイにわたって保持したり、再起動の前後で保持したりするには、コンテナー ファイル システムの外部にデータを永続化する必要があります。

既定では、パフォーマンスを向上させるために、メタデータのみが永続化され、イベントは引き続きメモリ内に格納されます。 イベントの永続化も有効にするには、「イベントの永続化」のセクションに従ってください。

この記事では、Linux デプロイで永続化を使用して Event Grid モジュールをデプロイする手順について説明します。

> [!NOTE]
>Event Grid モジュールは、UID `2000` と名前 `eventgriduser` を備えた低特権のユーザーとして実行されます。

## <a name="persistence-via-volume-mount"></a>ボリューム マウントによる永続化

 [Docker ボリューム](https://docs.docker.com/storage/volumes/)を使用して、デプロイ間でデータを保持します。 Event Grid モジュールをデプロイする際に、Docker で自動的に名前付きボリュームを作成することができます。 このオプションは、最も簡単なオプションです。 次のように、**Binds** セクションで、作成するボリュームの名前を指定できます。

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>バインド値の 2 番目の部分は変更しないでください。 これはモジュール内の特定の場所を指します。 Linux の Event Grid モジュールでは、これは **/app/metadataDb** である必要があります。

たとえば次の構成では、**egmetadataDbVol** というボリュームが作成され、ここでメタデータが永続化されます。

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

ボリュームをマウントする代わりに、ホスト システム上にディレクトリを作成し、そのディレクトリをマウントすることもできます。

## <a name="persistence-via-host-directory-mount"></a>ホスト ディレクトリ マウントによる永続化

Docker ボリュームの代わりに、ホスト フォルダーをマウントするオプションもあります。

1. まず、次のコマンドを実行して、名前が **eventgriduser**、ID が **2000** というユーザーをホスト マシン上に作成します。

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 次のコマンドを実行して、ホスト ファイル システム上にディレクトリを作成します。

   ```sh
   md <your-directory-name-here>
   ```

    たとえば、次のコマンドを実行すると、**myhostdir** という名前のディレクトリが作成されます。

    ```sh
    md /myhostdir
    ```
1. 次に、下のコマンドを実行して、**eventgriduser** をこのフォルダーの所有者にします。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    たとえば、次のように入力します。

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. **Binds** を使用してディレクトリをマウントし、Azure portal から Event Grid モジュールを再デプロイします。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    たとえば、次のように入力します。

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >バインド値の 2 番目の部分は変更しないでください。 これはモジュール内の特定の場所を指します。 Linux の Event Grid モジュールでは、これは **/app/metadataDb** および **/app/eventsDb** である必要があります


## <a name="persist-events"></a>イベントの永続化

イベントの永続化を有効にするには、最初に上記のセクションを使用して、ボリューム マウントまたはホスト ディレクトリ マウントのいずれかでメタデータの永続化を有効にする必要があります。

イベントの永続化に関する重要な注意事項: 

* イベントの永続化はイベント サブスクリプションごとに有効になり、ボリュームまたはディレクトリがマウントされるとオプトインされます。
* イベントの永続化は、作成時にイベント サブスクリプションで構成され、イベント サブスクリプションの作成後は変更できません。 イベントの永続化を切り替えるには、イベント サブスクリプションを削除してから再作成する必要があります。
* イベントの永続化は、ほとんどの場合、メモリ操作よりも低速ですが、速度の違いはドライブの特性に大きく依存します。 速度と信頼性のトレードオフは、すべてのメッセージング システムに内在しますが、一般的に大規模な場合にのみ顕著になります。

イベント サブスクリプションでイベントの永続化を有効にするには、`persistencePolicy` を `true` に設定します。

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
