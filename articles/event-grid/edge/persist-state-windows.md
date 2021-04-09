---
title: Windows で状態を永続化する - Azure Event Grid IoT Edge | Microsoft Docs
description: Windows で状態を永続化する
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: f38e23a3af1e2c81ee012a4f3c268cbff3fc1bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96002819"
---
# <a name="persist-state-in-windows"></a>Windows で状態を永続化する

Event Grid モジュールで作成したトピックとサブスクリプションは、既定でコンテナー ファイル システムに格納されます。 作成したメタデータが永続化されていない場合、モジュールを再デプロイするとすべて失われます。 複数のデプロイや再起動にわたってデータを保持するには、コンテナー ファイル システムの外部にデータを永続化する必要があります。 

既定では、パフォーマンスを向上させるために、メタデータのみが永続化され、イベントは引き続きメモリ内に格納されます。 イベントの永続化も有効にするには、「イベントの永続化」のセクションに従ってください。

この記事では、Windows デプロイで永続化を使用して Event Grid モジュールをデプロイするために必要な手順について説明します。

> [!NOTE]
>Event Grid モジュールは、Windows では低特権のユーザー **ContainerUser** として実行されます。

## <a name="persistence-via-volume-mount"></a>ボリューム マウントによる永続化

複数のデプロイにわたってデータを保持するために、[Docker ボリューム](https://docs.docker.com/storage/volumes/)が使用されます。 ボリュームをマウントするには、それを docker コマンドを使用して作成し、コンテナーが読み取りと書き込みをできるようにアクセス許可を付与してから、モジュールをデプロイする必要があります。

1. 次のコマンドを実行して、ボリュームを作成します。

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    たとえば、次のように入力します。

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 次のコマンドを実行して、ボリュームがマップされているホスト ディレクトリを取得します

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    たとえば、次のように入力します。

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   サンプル出力:

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 次のように、**Users** グループを **Mountpoint** によって示されている値に追加します。
    1. エクスプローラーを起動します。
    1. **Mountpoint** によって示されているフォルダーに移動します。
    1. 右クリックし、**[プロパティ]** を選択します。
    1. **[セキュリティ]** を選択します。
    1. [グループ名またはユーザー名] の下の **[編集]** を選択します。
    1. **[追加]** を選択し、「`Users`」と入力します。**[名前の確認]** を選択し、**[OK]** を選択します。
    1. *[Users のアクセス許可]* で、**[変更]** を選択し、**[OK]** を選択します。
1. **Binds** を使用してこのボリュームをマウントし、Azure portal から Event Grid モジュールを再デプロイします

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >バインド値の 2 番目の部分は変更しないでください。 これは、モジュール内の特定の場所を指します。 Windows 上の Event Grid モジュールの場合は、**C:\\app\\metadataDb** である必要があります。


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>ホスト ディレクトリ マウントによる永続化

ボリュームをマウントする代わりに、ホスト システム上にディレクトリを作成し、そのディレクトリをマウントすることもできます。

1. 次のコマンドを実行して、ホスト ファイル システム上にディレクトリを作成します。

   ```sh
   mkdir <your-directory-name-here>
   ```

   たとえば、次のように入力します。

   ```sh
   mkdir C:\myhostdir
   ```
1. **Binds** を使用してディレクトリをマウントし、Azure portal から Event Grid モジュールを再デプロイします。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >バインド値の 2 番目の部分は変更しないでください。 これは、モジュール内の特定の場所を指します。 Windows 上の Event Grid モジュールの場合は、**C:\\app\\metadataDb** である必要があります。

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>イベントの永続化

イベントの永続化を有効にするには、最初に上記のセクションを使用して、ボリューム マウントまたはホスト ディレクトリ マウントのいずれかでイベントの永続化を有効にする必要があります。

イベントの永続化に関する重要な注意事項: 

* イベントの永続化はイベント サブスクリプションごとに有効になり、ボリュームまたはディレクトリがマウントされるとオプトインされます。
* イベントの永続化は、作成時にイベント サブスクリプションで構成され、イベント サブスクリプションの作成後は変更できません。 イベントの永続化を切り替えるには、イベント サブスクリプションを削除してから再作成する必要があります。
* イベントの永続化は、ほとんどの場合、メモリ操作よりも低速ですが、速度の違いはドライブの特性に大きく依存します。 速度と信頼性の間のトレードオフは、すべてのメッセージング システムに固有のものですが、大規模な場合でのみ顕著になります。

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