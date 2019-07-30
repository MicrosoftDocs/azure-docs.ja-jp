---
title: Azure Media Services の Azure Storage アカウント | Microsoft Docs
description: Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/01/2019
ms.author: juliako
ms.openlocfilehash: 5c5bfa224b87040f5142663e6adab01072c6e6ba
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619293"
---
# <a name="azure-storage-accounts"></a>Azure Storage アカウント

Azure でメディア コンテンツの管理、暗号化、エンコード、分析、およびストリーミングを開始するには、Media Services アカウントを作成する必要があります。 Media Services アカウントの作成では、Azure Storage アカウント リソースの名前を指定する必要があります。 指定されたストレージ アカウントは、Media Services アカウントに関連付けられます。 

Media Services アカウントおよび関連するすべてのストレージ アカウントは、同じ Azure サブスクリプションに存在する必要があります。 待機時間やデータ送信コストが増加することを回避するために、ストレージ アカウントを Media Services アカウントと同じ場所で使用することを強くお勧めします。

1 つの**プライマリ** ストレージ アカウントを持つ必要があります。Media Services アカウントに関連付けられた任意の数の **セカンダリ** ストレージ アカウントを持つことができます。 Media Services は、**汎用 v2** (GPv2) アカウントまたは**汎用 v1** (GPv1) アカウントをサポートします。 <br/>BLOB のみのアカウントを**プライマリ**として使用することはできません。 

最新の機能とパフォーマンスを利用できるように、GPv2 を使用することをお勧めします。 ストレージ アカウントの詳細については、「[Azure ストレージ アカウントの概要](../../storage/common/storage-account-overview.md)」を参照してください。

> [!NOTE]
> Azure Media Services での使用をサポートされているのはホット アクセス層だけですが、他のアクセス層を使用してアクティブに使われていないコンテンツのストレージ コストを削減できます。

ストレージ アカウント用に選択できる SKU はいくつかあります。 詳細については、[ストレージ アカウント](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest)に関するページを参照してください。 ストレージ アカウントで実験する場合は、`--sku Standard_LRS` を使用します。 ただし、運用環境用の SKU を選択する場合は、ビジネス継続性のために地理的レプリケーションを提供する `--sku Standard_RAGRS` を検討してください。 

## <a name="assets-in-a-storage-account"></a>ストレージ アカウント内の資産

Media Services v3 では、Storage API シリーズを使用してファイルをアセットにアップロードします。 詳細については、[アセットの概念](assets-concept.md)に関するページを参照してください。

> [!Note]
> Media Service API を使用せずに Media Services SDK によって生成された BLOB コンテナーの内容は、変更しないでください。
 
## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services v3 でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v3|
|---|---|---|
|Media Services のストレージの暗号化| AES-256 暗号化、Media Services によって管理されるキー|サポートされていません<sup>(1)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|

<sup>1</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

## <a name="storage-account-errors"></a>ストレージ アカウント エラー

Media Services アカウントの「切断」状態は、ストレージ アクセス キーの変更が原因で、そのアカウントが、アタッチされたストレージ アカウントのうちの 1 つ以上にアクセスできなくなったことを示します。 アカウント内の多くのタスクを実行するには、Media Services に最新のストレージ アクセス キーが必要です。

アタッチされたストレージ アカウントに Media Services アカウントがアクセスできなくなる主なシナリオを次に示します。 

|問題|解決策|
|---|---|
|Media Services アカウントまたはアタッチされたストレージ アカウントが別々のサブスクリプションに移行された。 |ストレージ アカウントまたは Media Services アカウントがすべて同じサブスクリプション内に含まれるように、アカウントを移行してください。 |
|Media Services アカウントは、これがサポートされていたのが初期の Media Services アカウントであったため、別のサブスクリプションのアタッチされたストレージ アカウントを使用している。 初期の Media Services アカウントはすべて最新の Azure Resources Manager (ARM) ベースのアカウントに変換されたので、切断状態になります。 |ストレージ アカウントまたは Media Services アカウントがすべて同じサブスクリプション内に含まれるように、アカウントを移行してください。|

## <a name="next-steps"></a>次の手順

ストレージ アカウントを Media Services アカウントに関連付ける方法については、[アカウントの作成](create-account-cli-quickstart.md)に関する記事を参照してください。
