---
title: Azure Media Indexer 用のタスク プリセット
description: このトピックでは、Azure Media Indexer のタスク プリセットの概要について説明します。
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: a9a47f970f0f934e0953bd5e2d6e5575758a9c1c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67873503"
---
# <a name="task-preset-for-azure-media-indexer"></a>Azure Media Indexer 用のタスク プリセット 

Azure Media Indexer はメディア プロセッサであり、メディア ファイルとコンテンツを検索できるようにする、クローズド キャプションのトラックとキーワードを生成する、資産に含まれる資産ファイルのインデックスを作成する、といったタスクの実行に使われます。

このトピックでは、インデックス作成ジョブに渡す必要があるタスク プリセットについて説明します。 完全な例については、「[Azure Media Indexer によるメディア ファイルのインデックス作成](media-services-index-content.md)」をご覧ください。

## <a name="azure-media-indexer-configuration-xml"></a>Azure Media Indexer 構成 XML

次の表では、構成 XML の要素と属性について説明します。

|EnableAdfsAuthentication|必須|説明|
|---|---|---|
|入力|true|インデックスの対象となるアセット ファイル。<br/>Azure Media Indexer は、メディア ファイル形式としてMP4、MOV、WMV、MP3、M4A、WMA、AAC、WAV をサポートしています。 <br/><br/>**input** 要素の **name** または **list** 属性で、ファイル名を指定できます (後述参照)。 インデックスを付ける資産ファイルを指定しないと、プライマリ ファイルが選ばれます。 プライマリ資産ファイルが設定されていない場合は、入力資産の 1 つ目のファイルのインデックスが作成されます。<br/><br/>資産ファイル名を明示的に指定するには、次を実行します。<br/>```<input name="TestFile.wmv" />```<br/><br/>複数の資産ファイルのインデックスを一度に作成することもできます (最大 10 ファイル)。 これを行うには、次の手順を実行します。<br/>- テキスト ファイル (マニフェスト ファイル) を作成し、.lst という拡張子を指定します。<br/>- 入力資産に含まれるすべての資産ファイルの名前をこのマニフェスト ファイルに追加します。<br/>- マニフェスト ファイルを資産に追加 (アップロード) します。<br/>- マニフェスト ファイルの名前を input の list 属性に指定します。<br/>```<input list="input.lst">```<br/><br/>**注:** マニフェスト ファイルに 10 個を超えるファイルを追加すると、インデックス作成ジョブが 2006 エラー コードで失敗します。|
|metadata|false|指定した資産ファイルのメタデータです。<br/>```<metadata key="..." value="..." />```<br/><br/>事前定義済みのキーに対して値を指定できます。 <br/><br/>現在サポートされているキーは次のとおりです。<br/><br/>**title**、**description** - 言語モデルを更新して音声認識の精度を向上させるために使われます。<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**username**、**password** - http または https でインターネット ファイルをダウンロードするときの認証に使われます。<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>username と password の値は、入力マニフェストのすべてのメディア URL に適用されます。|
|features<br/><br/>バージョン 1.2 で追加。 現時点でサポートされている機能は、音声認識 ("ASR") のみです。|false|音声認識機能には、次の設定キーがあります。<br/><br/>Language:<br/>- マルチメディア ファイル内で認識される自然言語。<br/>- English、Spanish<br/><br/>CaptionFormats:<br/>- 出力キャプション形式をセミコロンで区切ったリスト (存在する場合)<br/>- ttml;sami;webvtt<br/><br/><br/>GenerateAIB:<br/>- (SQL Server とお客様の Indexer IFilter で使用するために) AIB ファイルが必要かどうかを指定するブール値のフラグ。 詳しくは、「AIB ファイルを Azure Media Indexer および SQL Server で使用する」をご覧ください。<br/>- True; False<br/><br/>GenerateKeywords:<br/>- キーワード XML ファイルが必要かどうかを指定するブール型のフラグ。<br/>- True; False|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer 構成 XML の例

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="CaptionFormats" value="ttml;sami;webvtt"/>  
        <add key="GenerateAIB" value ="true" />  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>次の手順

「[Azure Media Indexer によるメディア ファイルのインデックス作成](media-services-index-content.md)」をご覧ください。

