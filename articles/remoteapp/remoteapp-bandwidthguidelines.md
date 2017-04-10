---
title: "Azure RemoteApp で使用されるネットワーク帯域幅 - 一般的なガイドライン | Microsoft Docs"
description: "Azure RemoteApp コレクションおよびアプリで使用されるネットワーク帯域幅に関する基本的なガイドラインを紹介します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 529bf318-ae37-4c14-a11c-43fa703d68a3
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b44fcb6cca30acbd5771a0301f72faa7d9105849


---
# <a name="azure-remoteapp-network-bandwidth---general-guidelines-if-you-cant-test-your-own"></a>Azure RemoteApp ネットワークで使用される帯域幅 - 一般的なガイドライン (自分でテストできない場合)
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

Azure RemoteApp の [ネットワーク帯域幅テスト](remoteapp-bandwidthtests.md) を実施する余裕がない場合は、下記の汎用的なガイドラインを参考に、ユーザーごとのネットワーク帯域幅を見積もることができます。

以下のシナリオが混在している場合は、インターネットに接続された最新のアプリをリモート環境で利用するうえで、最低ネットワーク帯域幅が 10 MB/秒以下の環境はお勧めしません (ただし、既に説明したとおり、平均的なユーザー エクスペリエンスを上回る保証はありません)。

## <a name="complex-powerpoint-simple-powerpoint"></a>複雑な PowerPoint、単純な PowerPoint
Azure RemoteApp のパフォーマンスは、100 MB の LAN で最も高くなります。 10 MB/秒のネットワーク プロファイルであれば、120 ミリ秒を超えるジッターが 5% を上回っていても、平均的なエクスペリエンスとなります。 1 MB/秒の場合は状況が変わります。たとえば、スライド ショーの際に切り替えのアニメーションが見えなくなる可能性があります。フレームがスキップされるためです。

## <a name="internet-explorer-mixed-pdf-pdf-text"></a>Internet Explorer、混合 PDF、PDF、テキスト
10 MB/秒のネットワーク プロファイルであれば、ほとんどの面で LAN に迫るユーザー エクスペリエンスとなります。 1 MB/秒でも特に問題なく使えますが、画面上に画像がある状態でスクロールすると、ジッターが多少発生する可能性があります。

## <a name="flash-video-youtube"></a>Flash ビデオ (YouTube)
100 MB/秒の LAN で最も快適に視聴できますが、10 MB/秒でも問題ありません (この場合、フレーム レートは維持されますが、ジッターが増加します)。 1 MB/秒では、ジッターがかなり増え、目立つようになります。

## <a name="word-typing-word-remote-input"></a>Word の入力 (Word のリモート入力)
こちらは低い帯域幅での使用を想定しています。 256 KB/秒で、LAN と同等の感覚で快適に使用できます。

## <a name="learn-more"></a>詳細情報
* [Estimate Azure RemoteApp network bandwidth usage (Azure RemoteApp で使用されるネットワーク帯域幅を推定する)](remoteapp-bandwidth.md)
* [Azure RemoteApp - ネットワーク帯域幅とエクスペリエンスの質はどのような関係にあるのか](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp - 一般的なシナリオでのネットワークの使用帯域幅をテストする](remoteapp-bandwidthtests.md)




<!--HONumber=Nov16_HO3-->


