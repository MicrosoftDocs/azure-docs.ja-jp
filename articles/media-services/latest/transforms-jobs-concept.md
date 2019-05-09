---
title: Azure Media Services の Transform と Job | Microsoft Docs
description: Media Services を使用するときは、ビデオを処理するためのルールまたは仕様を説明する Transform を作成する必要があります。 この記事では、Transform とその使用方法の概要を示します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/20/2019
ms.author: juliako
ms.openlocfilehash: 1c2ec576211741390ef91233101261a7881e4180
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652214"
---
# <a name="transforms-and-jobs"></a>Transform と Job

このトピックでは、[Transform](https://docs.microsoft.com/rest/api/media/transforms) と [Job](https://docs.microsoft.com/rest/api/media/jobs) に関する詳細を示し、これらのエンティティ間の関係について説明します。 次の図では変換/ジョブ ワークフローを示します。

![変換](./media/encoding/transforms-jobs.png)

> [!NOTE]
> Datetime 型である **Transform** および **Job** のプロパティは、常に UTC 形式です。

## <a name="transforms"></a>変換

**Transform** を使用して、ビデオのエンコードや分析を行うための一般的なタスクを構成できます。 各 **Transform** は、ビデオまたはオーディオ ファイルを処理するためのレシピ､すなわちタスクのワークフローの記述です｡ 1 つの Transform によって複数のルールを適用することができます｡ たとえば､1 つの Transform で各ビデオを指定されたビットレートの MP4 ファイルにエンコードして､そのビデオの先頭フレームからサムネール画像を生成するように指定できます｡ Transform に含めたいルールごとに TransformOutput エントリ 1 つを追加します｡ Transform は､Media Services v3 API を使用して､または公開されている任意の SDK を使用して､Media Services アカウントに作成することができます｡ Media Services v3 の API は Azure Resource Manager によって実行されるため、Resource Manager テンプレートを使用して、Media Services アカウントに Transforms を作成して デプロイすることもできます｡ ロールベースのアクセス制御を使用して､Transforms へのアクセスを管理することができます｡

[Transform](https://docs.microsoft.com/rest/api/media/transforms) エンティティの更新操作は、基になる TransformOutput の記述または優先度を変更することを目的とします。 このような更新は、進行中のすべてのジョブが完了したときに実行することをお勧めします。 レシピを書き直す場合は､新しい Transform を作成する必要があります。

## <a name="jobs"></a>[ジョブ]

各 **Job** は、与えられた入力ビデオまたはオーディオ コンテンツに **Transform** を適用する Azuru Media Services への実際の要求です。 Transform を作成すると､Media Services API または公開されている任意の SDK を使用してジョブを送信できます｡ **ジョブ** には、入力ビデオの場所や出力先などの情報を指定します。 入力ビデオの場所は、HTTPS URL、SAS URL、または[アセット](https://docs.microsoft.com/rest/api/media/assets)を使用して指定できます。  

既に URL 経由でコンテンツにアクセスでき、ソース ファイルを Azure に格納する必要がない場合 (たとえば、S3 からインポートするなど)、[HTTPS からのジョブ入力](job-input-from-http-how-to.md)を使用します。 また、コンテンツは Azure BLOB ストレージ内にあるが、資産の中にファイルがなくてもかまわない場合にも、この方法が適しています。 現時点で、この方法では、入力として 1 ファイルのみをサポートしています。
 
入力コンテンツが既に資産の中にあるか、またはコンテンツがローカル ファイルに格納されている場合は、[ジョブ入力としての資産](job-input-from-local-file-how-to.md)を使用します。 また、入力した資産をストリーミングまたはダウンロード用に公開することを計画している (つまり、ダウンロード用に mp4 を公開すると共に、音声テキスト変換や顔検出も行いたい) 場合にも、適切なオプションになります。 この方法では、複数ファイルの資産をサポートします (たとえば、ローカルにエンコードされた MBR ストリーミング セットなど)。
 
ジョブの進捗状況と状態は､Event Grid でイベントを監視することによって取得できます｡ 詳しくは､｢[EventGrid を使ってイベントを監視する](job-state-events-cli-how-to.md)｣を参照してください｡

ジョブが送信された後、[Job](https://docs.microsoft.com/rest/api/media/jobs) エンティティの更新操作を使用して、*description* および *priority* プロパティを変更できます。 *priority* プロパティの変更は、ジョブがキューに入っている状態の場合にのみ有効です。 ジョブの処理がすでに開始されているか､完了している場合､優先順位の変更は適用されません｡

## <a name="typical-workflow"></a>一般的なワークフロー

1. Transform を作成します。 
2. その Transform に基づいて Job を送信します。 
3. Transform を一覧表示します。 
4. 将来利用する予定のない Transform は削除してください｡ 

### <a name="example"></a>例

すべてのビデオの最初のフレームをサムネイル画像として抽出するとします。実行する手順は次のようになります。 

1. ｢ビデオの先頭フレームをサムネールとして使用する｣などのビデオを処理するレシピ､すなわちルールを定義します｡ 
2. 1 つのビデオについて､サービスに次の情報を提供します｡ 
    1. ビデオがある場所｡  
    2. 出力するサムネイル画像を書き込む場所｡ 

**Transform** でレシピを 1 回作成すると (手順 1)､そのレシピを使って複数のジョブを送信することができます (手順 2)｡

## <a name="job-error-codes"></a>ジョブ エラー コード

[エラー コード](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)に関するページを参照してください。

## <a name="paging"></a>ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="configure-media-reserved-units"></a>メディア占有ユニットを構成する

Media Services v3 または Video Indexer によってトリガーされる音声分析と動画分析ジョブでは、お使いのアカウントを 10 個の S3 のメディア占有ユニット (MRU) でプロビジョニングすることを強くお勧めします。 10 個を超える S3 の MRU が必要な場合は、[Azure portal](https://portal.azure.com/) を使用してサポート チケットを開いてください。

詳細については、「[CLI を使用したメディア処理のスケーリング](media-reserved-units-cli-how-to.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [チュートリアル:.NET を使用してビデオをアップロード、エンコード、ストリーム配信する](stream-files-tutorial-with-api.md)
- [チュートリアル:.NET を使用して Media Services v3 でビデオを分析する](analyze-videos-tutorial-with-api.md)
