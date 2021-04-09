---
title: Python を使用した Azure Storage サンプル | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 Python のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/01/2020
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.custom: devx-track-python
ms.openlocfilehash: f57fa1b668cacdaaceb2026f9e5d7fff0cc56d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371360"
---
# <a name="azure-storage-samples-using-v12-python-client-libraries"></a>v12 Python クライアント ライブラリを使用した Azure Storage サンプル

次の表は、各サンプルで扱っているサンプル リポジトリとシナリオの概要を示したものです。 リンクをクリックすると、対応するサンプル コードが GitHub で表示されます。

> [!NOTE]
> これらのサンプルには、最新の Azure Storage .NET v12 ライブラリが使用されています。 従来の v2.1 のコードについては、GitHub リポジトリの「[Azure Storage: Python での Azure Storage の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started)」を参照してください。

## <a name="blob-samples"></a>BLOB のサンプル

### <a name="authentication"></a>認証

:::row:::
   :::column span="":::
      [接続文字列を使用して Blob service クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L45)
   :::column-end:::
   :::column span="":::
      [接続文字列を使用してコンテナー クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L50)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [接続文字列を使用して BLOB クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L56)
   :::column-end:::
   :::column span="":::
      [共有アクセス キーを使用して Blob service クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L66)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [URL から BLOB クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L75)
   :::column-end:::
   :::column span="":::
      [SAS URL から BLOB クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ClientSecretCredential を使用して Blob service クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L88)
   :::column-end:::
   :::column span="":::
      [SAS トークンを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L110)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Azure Identity を使用して Blob service クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_authentication.py#L125)
   :::column-end:::
   :::column span="":::
      [BLOB スナップショットを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L56)
   :::column-end:::
:::row-end:::

### <a name="blob-service"></a>Blob service

:::row:::
   :::column span="":::
      [Blob service のアカウント情報を取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L30)
   :::column-end:::
   :::column span="":::
      [Blob service のプロパティを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L41)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Blob service のプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L61)
   :::column-end:::
   :::column span="":::
      [Blob service の統計情報を取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L71)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [サービス クライアントを使用してコンテナーを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L82)
   :::column-end:::
   :::column span="":::
      [コンテナーをリストする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L90)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [サービス クライアントを使用してコンテナーを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L103)
   :::column-end:::
   :::column span="":::
      [コンテナー クライアントを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L117)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [BLOB クライアントを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_service.py#L130)
   :::column-end:::
:::row-end:::

### <a name="container"></a>コンテナー

:::row:::
   :::column span="":::
      [サービスからコンテナー クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L35)
   :::column-end:::
   :::column span="":::
      [SAS URL を使用してコンテナー クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L44)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [コンテナー クライアントを使用してコンテナーを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L52)
   :::column-end:::
   :::column span="":::
      [コンテナーのプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L56)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [コンテナー クライアントを使用してコンテナーを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L61)
   :::column-end:::
   :::column span="":::
      [コンテナーのリースを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L80)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [コンテナー メタデータを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L101)
   :::column-end:::
   :::column span="":::
      [コンテナーのアクセス ポリシーを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L128)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [コンテナーのアクセス ポリシーを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L141)
   :::column-end:::
   :::column span="":::
      [SAS トークンを生成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L145)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [SAS トークンを使用してコンテナー クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L158)
   :::column-end:::
   :::column span="":::
      [コンテナーに BLOB をアップロードする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L182)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [コンテナー内の BLOB をリストする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L189)
   :::column-end:::
   :::column span="":::
      [BLOB クライアントを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_containers.py#L213)
   :::column-end:::
:::row-end:::

### <a name="blob"></a>BLOB

:::row:::
   :::column span="":::
      [BLOB をアップロードする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L68)
   :::column-end:::
   :::column span="":::
      [BLOB をダウンロードする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L74)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB を削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_hello_world.py#L80)
   :::column-end:::
   :::column span="":::
      [BLOB の削除を取り消す](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L100)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB のプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L105)
   :::column-end:::
   :::column span="":::
      [複数の BLOB を削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L133)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [BLOB を URL からコピーする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L193)
   :::column-end:::
   :::column span="":::
      [URL からの BLOB のコピーを中止する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L205)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [BLOB のリースを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/samples/blob_samples_common.py#L167)
   :::column-end:::
:::row-end:::

## <a name="data-lake-storage-gen2-samples"></a>Data Lake Storage Gen2 のサンプル

### <a name="data-lake-service"></a>Data Lake サービス

:::row:::
   :::column span="2":::
      [Data Lake サービス クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L64)
   :::column-end:::
:::row-end:::

### <a name="file-system"></a>ファイル システム

:::row:::
   :::column span="":::
      [ファイル システム クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L74)
   :::column-end:::
   :::column span="":::
      [ファイル システムを削除する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L81)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>ディレクトリ

:::row:::
   :::column span="":::
      [ディレクトリ クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L14)
   :::column-end:::
   :::column span="":::
      [ディレクトリのアクセス許可を取得する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L19)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ディレクトリのアクセス許可を設定する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L23)
   :::column-end:::
   :::column span="":::
      [ディレクトリ名を変更する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L19)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ディレクトリのプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L25)
   :::column-end:::
   :::column span="":::
      [ディレクトリの削除](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_directory.py#L29)
   :::column-end:::
:::row-end:::

### <a name="file"></a>ファイル

:::row:::
   :::column span="":::
      [ファイル クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L13)
   :::column-end:::
   :::column span="":::
      [ファイルを作成する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイルのアクセス許可を取得する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L35)
   :::column-end:::
   :::column span="":::
      [ファイルのアクセス許可を設定する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control.py#L38)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイルにデータを追加する](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L18)
   :::column-end:::
   :::column span="":::
      [ファイルからデータを読み取る](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_upload_download.py#L29)
   :::column-end:::
:::row-end:::

## <a name="azure-files-samples"></a>Azure Files のサンプル

### <a name="authentication"></a>認証

:::row:::
   :::column span="":::
      [接続文字列から共有サービス クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L42)
   :::column-end:::
   :::column span="":::
      [アカウントとアクセス キーから共有サービス クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [SAS トークンを生成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_authentication.py#L59)
   :::column-end:::
:::row-end:::

### <a name="file-service"></a>File service

:::row:::
   :::column span="":::
      [サービスのプロパティを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L39)
   :::column-end:::
   :::column span="":::
      [サービスのプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L68)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイル サービス クライアントを使用して共有を作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L77)
   :::column-end:::
   :::column span="":::
      [ファイル サービス クライアントを使用して共有をリストする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L81)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [ファイル サービス クライアントを使用して共有を削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L91)
   :::column-end:::
:::row-end:::

### <a name="file-share"></a>ファイル共有

:::row:::
   :::column span="":::
      [接続文字列から共有クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L52)
   :::column-end:::
   :::column span="":::
      [共有クライアントを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_service.py#L96)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイル共有クライアントを使用して共有を作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L39)
   :::column-end:::
   :::column span="":::
      [共有スナップショットを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L43)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイル共有クライアントを使用して共有を削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L47)
   :::column-end:::
   :::column span="":::
      [共有のクォータを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L61)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [共有のメタデータを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L66)
   :::column-end:::
   :::column span="":::
      [共有のプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py#L47)
   :::column-end:::
:::row-end:::

### <a name="directory"></a>ディレクトリ

:::row:::
   :::column span="":::
      [ディレクトリの作成](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L45)
   :::column-end:::
   :::column span="":::
      [ディレクトリにファイルをアップロードする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L49)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ディレクトリからファイルを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L55)
   :::column-end:::
   :::column span="":::
      [ディレクトリの削除](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L60)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [サブディレクトリを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L80)
   :::column-end:::
   :::column span="":::
      [ディレクトリとファイルをリストする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L96)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [サブディレクトリを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L104)
   :::column-end:::
   :::column span="":::
      [サブディレクトリ クライアントを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_directory.py#L121)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [ディレクトリ内のファイルをリストする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_share.py#L87)
   :::column-end:::
:::row-end:::

### <a name="file"></a>ファイル

:::row:::
   :::column span="":::
      [ファイル クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_hello_world.py#L65)
   :::column-end:::
   :::column span="":::
      [ファイルを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L48)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイルをアップロードする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L54)
   :::column-end:::
   :::column span="":::
      [ファイルをダウンロードする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L60)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [ファイルの削除](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L67)
   :::column-end:::
   :::column span="":::
      [URL からファイルをコピーする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-share/samples/file_samples_client.py#L101)
   :::column-end:::
:::row-end:::

## <a name="queue-samples"></a>キューのサンプル

### <a name="authentication"></a>認証

:::row:::
   :::column span="":::
      [接続文字列を使用して認証する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L50)
   :::column-end:::
   :::column span="":::
      [Queue サービス クライアントのトークンを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L69)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [接続文字列からキュー クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L35)
   :::column-end:::
   :::column span="":::
      [キュー クライアントの SAS トークンを生成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L61)
   :::column-end:::
:::row-end:::

### <a name="queue-service"></a>Queue サービス

:::row:::
   :::column span="":::
      [Queue サービス クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_authentication.py#L60)
   :::column-end:::
   :::column span="":::
      [Queue サービスのプロパティを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L35)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [Queue サービスのプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L67)
   :::column-end:::
   :::column span="":::
      [サービス クライアントを使用してキューを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L76)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [サービス クライアントを使用してキューを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L94)
   :::column-end:::
:::row-end:::

### <a name="queue"></a>キュー

:::row:::
   :::column span="":::
      [キュー クライアントを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L72)
   :::column-end:::
   :::column span="":::
      [キューのメタデータを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L95)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [キューのプロパティを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L100)
   :::column-end:::
   :::column span="":::
      [キュー クライアントを使用してキューを作成する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_hello_world.py#L45)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [キュー クライアントを使用してキューを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_hello_world.py#L62)
   :::column-end:::
   :::column span="":::
      [キューをリストする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L81)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [キュー クライアントを取得する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_service.py#L103)
   :::column-end:::
:::row-end:::

### <a name="message"></a>Message

:::row:::
   :::column span="":::
      [メッセージの送信](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L117)
   :::column-end:::
   :::column span="":::
      [メッセージを受信する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L125)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [メッセージをピークする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L197)
   :::column-end:::
   :::column span="":::
      [メッセージを更新する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L222)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [メッセージを削除する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L165)
   :::column-end:::
   :::column span="":::
      [メッセージをクリアする](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L173)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [メッセージのアクセス ポリシーを設定する](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-queue/samples/queue_samples_message.py#L47)
   :::column-end:::
:::row-end:::


## <a name="table-samples-sdk-v21"></a>テーブルのサンプル (SDK v2.1)

:::row:::
   :::column span="":::
      [テーブルを作成する](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46)
   :::column-end:::
   :::column span="":::
      [エンティティまたはテーブルを削除する](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [エンティティを挿入、マージ、置換する](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57)
   :::column-end:::
   :::column span="":::
      [エンティティを照会する](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="":::
      [テーブルを照会する](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py)
   :::column-end:::
   :::column span="":::
      [テーブルの ACL またはプロパティ](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      [エンティティを更新する](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68)
   :::column-end:::
:::row-end:::

## <a name="azure-code-sample-libraries"></a>Azure のコード サンプル ライブラリ

完全な Python サンプル ライブラリをご覧いただくには、次のページにアクセスしてください。

* [Azure BLOB のコード サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)
* [Azure Data Lake のコード サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Azure Files のコード サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-share/samples)
* [Azure Queue のコード サンプル](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples)

各ライブラリの GitHub リポジトリを参照してクローンすることができます。

## <a name="getting-started-guides"></a>概要ガイド

Azure Storage クライアント ライブラリのインストール方法と概要については、以下のガイドをご覧ください。

* [Python での Azure Blob service の概要](../blobs/storage-quickstart-blobs-python.md)
* [Python での Azure Queue サービスの概要](../queues/storage-quickstart-queues-python.md)
* [Python での Azure Table service の概要](../../cosmos-db/table-storage-how-to-use-python.md)
* [Python での Azure File service の概要](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>次のステップ

他の言語のサンプルについては、以下のページをご覧ください。

* .NET:[.NET を使用した Azure Storage サンプル](storage-samples-dotnet.md)
* Java:[Java を使用した Azure Storage サンプル](storage-samples-java.md)
* JavaScript/Node.js: [JavaScript を使用した Azure Storage サンプル](storage-samples-javascript.md)
* C++: [C++ を使用した Azure Storage サンプル](storage-samples-c-plus-plus.md)
* その他すべての言語: [Azure Storage のサンプル](storage-samples.md)
