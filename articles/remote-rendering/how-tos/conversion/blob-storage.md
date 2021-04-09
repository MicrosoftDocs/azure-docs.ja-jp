---
title: モデルの変換に Azure Blob Storage を使用する
description: モデルの変換に BLOB ストレージを設定して使用するための一般的な手順について説明します。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: d8a6fd458cdcf79cdeb693b25acf72d4ec48def7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507519"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>モデルの変換に Azure Blob Storage を使用する

[モデルの変換](model-conversion.md)サービスでは、入力データを取得して出力データを格納できるように、Azure Blob Storage へのアクセスが必要です。 この記事では、それを行う最も一般的な手順について説明します。

## <a name="prepare-azure-storage-accounts"></a>Azure Storage アカウントを準備する

- ストレージ アカウント (StorageV2) を作成する
- そのストレージ アカウントで入力 BLOB コンテナー (たとえば、"arrinput" という名前) を作成する
- そのストレージ アカウントで出力 BLOB コンテナー (たとえば、"arroutput" という名前) を作成する

> [!TIP]
> ストレージ アカウントを設定する手順の詳細については、「[クイックスタート:モデルをレンダリング用に変換する](../../quickstarts/convert-model.md)」を参照してください。

ストレージ アカウントと BLOB コンテナーの作成は、次のいずれかのツールを使用して行うことができます。

- [Azure Portal](https://portal.azure.com)
- [az コマン ドライン](/cli/azure/install-azure-cli)
- [Azure 記憶域エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)
- SDK (C#、Python ...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Azure Remote Rendering が自分のストレージ アカウントにアクセスできることを確認する

Azure Remote Rendering では、ストレージ アカウントからモデル データを取得し、それにデータを書き戻す必要があります。

次の 2 通りの方法で、自分のストレージ アカウントへのアクセスを Azure Remote Rendering に許可できます。

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>自分の Azure Storage アカウントを Azure Remote Rendering アカウントに接続する

[アカウントの作成](../create-an-account.md#link-storage-accounts)に関するセクションに記載されている手順に従います。

### <a name="retrieve-sas-for-the-storage-containers"></a>ストレージ コンテナーの SAS を取得する

Shared Access Signature (SAS) を使用して、入力に対する読み取りアクセスと出力に対する書き込みアクセスを許可します。 モデルが変換されるたびに、新しい URI を生成することをお勧めします。 URI はしばらくすると期限切れになるため、これらを長期間保持すると、アプリケーションが予期せず中断する危険性があります。

SAS の詳細については、[SAS のドキュメント](../../../storage/common/storage-sas-overview.md)を参照してください。

SAS URI は、次のいずれかを使用して生成できます。

- az PowerShell モジュール
  - 「[PowerShell スクリプトの例](../../samples/powershell-example-scripts.md)」を参照
- [az コマン ドライン](/cli/azure/install-azure-cli)
- [Azure 記憶域エクスプローラー](https://azure.microsoft.com/features/storage-explorer/)
  - コンテナーを右クリックして [Shared Access Signature の取得] を選択する (入力コンテナーに対しては読み取りとリスト アクセス、出力コンテナーに対しては書き込みアクセス)
- SDK (C#、Python ...)

アセット変換での Shared Access Signature の使用例は、「[PowerShell スクリプトの例](../../samples/powershell-example-scripts.md#script-conversionps1)」の「Conversion.ps1」に示されています。

## <a name="upload-an-input-model"></a>入力モデルをアップロードする

モデルの変換を開始するには、次のいずれかのオプションを使用して、モデルをアップロードする必要があります。

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) - Azure Blob Storage でファイルをアップロード、ダウンロード、管理するための便利な UI
- [Azure コマンド ライン](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell モジュール](/powershell/azure/install-az-ps)
  - 「[PowerShell スクリプトの例](../../samples/powershell-example-scripts.md)」を参照
- [Storage SDK (Python、C# ...) の使用](../../../storage/index.yml)
- [Azure Storage REST API の使用](/rest/api/storageservices/blob-service-rest-api)

変換のためにデータをアップロードする方法の例については、「[PowerShell スクリプトの例](../../samples/powershell-example-scripts.md#script-conversionps1)」の「Conversion.ps1」を参照してください。

## <a name="get-a-sas-uri-for-the-converted-model"></a>変換されたモデルの SAS URI を取得する

この手順は、[ストレージ コンテナーの SAS を取得する](#retrieve-sas-for-the-storage-containers)のと似ています。 ただし、ここでは、出力コンテナーに書き込まれたモデル ファイルの SAS URI を取得する必要があります。

たとえば、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して SAS URI を取得するには、モデル ファイルを右クリックし、[Shared Access Signature の取得] を選択します。

自分のストレージ アカウントを Azure Remote Rendering アカウントに接続していない場合は、モデルを読み込むために Shared Access Signature (SAS) が必要です。 アカウントを接続する方法については、[アカウントの作成](../create-an-account.md#link-storage-accounts)に関するセクションを参照してください。

## <a name="next-steps"></a>次のステップ

- [モデル変換を構成する](configure-model-conversion.md)
- [モデル変換 REST API](conversion-rest-api.md)
