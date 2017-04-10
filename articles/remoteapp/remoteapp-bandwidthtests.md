---
title: "Azure RemoteApp - 一般的なシナリオでのネットワークの使用帯域幅をテストする | Microsoft Docs"
description: "Azure RemoteApp に必要なネットワーク帯域幅を算定するのに役立つ一般的な使用シナリオについて説明します。"
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 06417c75-0c63-4ecf-b9d1-66a7af6b7b91
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1729d9ba3f873800127ba3ca77676b843611fded


---
# <a name="azure-remoteapp---testing-your-network-bandwidth-usage-with-some-common-scenarios"></a>Azure RemoteApp - 一般的なシナリオでのネットワークの使用帯域幅をテストする
> [!IMPORTANT]
> Azure RemoteApp の提供は終了しました。 詳細については、 [お知らせ](https://go.microsoft.com/fwlink/?linkid=821148) をご覧ください。
> 
> 

「 [Estimate Azure RemoteApp network bandwidth usage (Azure RemoteApp で使用されるネットワーク帯域幅を推定する)](remoteapp-bandwidth.md)」で説明したとおり、Azure RemoteApp がネットワークに及ぼす影響を算定する最善の方法は、使用量のテストを実行することです。 特定の期間に対してこれらのテストを実行し、各シナリオに必要な帯域幅を測定します。 可能であれば、特定の環境で生じるネットワークのパターンを理解するために、ネットワーク パケットの損失やネットワークのジッターを測定することもできます。

使用帯域幅を評価するときには、社内のユーザーごとに使用量が異なることにご注意ください。 たとえば、テキスト リーダーとテキスト ライターが通常使用する帯域幅は、ビデオを利用するユーザーよりも少なくなります。 最善の結果を得るためには、ご自分のユーザーのニーズを調査し、社内のユーザーを最もよく表す次のシナリオの組み合わせをご作成ください。 [使用帯域幅とユーザー エクスペリエンスに影響する要因の確認](remoteapp-bandwidthexperience.md)を忘れずに行ってください。これは理想的なテストを特定するのに役立ちます。

最初にテストについて読んでから、組み合わせを選択して、それらを実行します。 以下の表を使用して、パフォーマンスを追跡できます。

> [!NOTE]
> ご自分でネットワーク テストを実行できない場合、またはそのための時間がない場合は、 [基本的なネットワーク帯域幅の推定/推奨事項](remoteapp-bandwidthguidelines.md)をご確認ください。 ただし、ご自分のマイレージは違う可能性があることから、ご自分でテストを実行 *できる* 場合には、そうする必要があります。
> 
> 

## <a name="the-usage-tests"></a>使用量のテスト
これらのテストの実行にかかる時間は、テストごとに異なります。ネットワーク帯域幅を使用するテスト対象機能も、テストごとに異なります。 社内の個々のユーザーに最適なテストの組み合わせをご選択ください。

### <a name="executivecomplex-powerpoint---run-for-900-1000-seconds"></a>エグゼクティブ用/複雑な PowerPoint - 実行時間 900 から 1000 秒
ユーザーは Microsoft Office PowerPoint を全画面表示モードで使用して、45 から 50 枚の高品質なスライドを表示します。 スライドには、イメージ、切り替え (アニメーション)、および自社の一般的なグラデーションによる背景が含まれる必要があります。 ユーザーは、各スライドに少なくとも 20 秒を費やす必要があります。

このシナリオでは、プレゼンテーションの次のスライドへのスライド切り替えのときに、バースト トラフィックが発生します。

### <a name="simple-powerpoint---run-for-620-seconds"></a>単純な PowerPoint - 実行時間 620 秒以内
ユーザーは、Microsoft Office PowerPoint を全画面表示モードで使用して、約 30 枚のスライドで構成される単純な PowerPoint ファイルを実行します。 これらのスライドには、エグゼクティブ用/複雑な PowerPoint シナリオより多くのテキストと、単純な背景およびイメージ (黒ダイアグラム) が含まれます。 

### <a name="internet-explorer---run-for-250-seconds"></a>Internet Explorer - 実行時間 250 秒以内
ユーザーは、Internet Explorer を使用して Web を参照します。 ユーザーは、テキスト、ナチュラル イメージ、概略図の組み合わせを参照およびスクロールします。 Web ページはリモート デスクトップ セッション ホスト (RD セッション ホスト) サーバーのローカル ディスク ドライブに .MHT ファイルとして格納されます。  ユーザーは PageUp、PageDown、上へ、および下への各キー使用してスクロールします。スクロールの各キー/種類の間隔は異なります。

    - Down - 250 keystrokes very 500 ms
    - Page Up - 36 keystrokes every 1000 ms
    - Down - 75 keystrokes every 100 ms
    - Page Down - 20 keystrokes every 500 ms
    - Up - 120 keystrokes every 300 ms

### <a name="pdf-document---simple---run-for-610-seconds"></a>PDF ドキュメント - 単純 - 実行時間 610 秒以内
ユーザーは、Adobe Acrobat Reader を使用して、さまざまな方法で PDF ドキュメントの読み取りおよび検索を行います。 ドキュメントはテーブル、単純なグラフ、および複数のテキスト フォントで構成される必要があります。 ドキュメントの長さは 35 から 40 ページです。 ユーザーは 4 種類のズーム サイズ (fit to page、fit to width、100%、その他の選択) を使用して、2 種類のスクロール速度で、順方向と逆方向にスクロールします。 ズームを使用することで、テキスト (フォント) をさまざまなサイズで表示できます。 PageUp、PageDown、上へ、および下への各キー使用してスクロールします。各スクロールの間隔は異なります。

### <a name="pdf-document---mixed---run-for-320-seconds"></a>PDF ドキュメント - 混合 - 実行時間 320 秒以内
ユーザーは、Adobe Acrobat Reader を使用して、さまざまな方法で PDF ドキュメントの読み取りおよび検索を行います。 ドキュメントは、高品質の画像 (写真を含む)、テーブル、単純なグラフ、および複数のテキストのフォントで構成されます。 ユーザーは 4 種類のズーム サイズ (fit to page、fit to width、100%、その他の選択) を使用して、2 種類のスクロール速度で、順方向と逆方向にスクロールします。 ズームを使用することで、テキスト (フォント) をさまざまなサイズで表示できます。 PageUp、PageDown、上へ、および下への各キー使用してスクロールします。各スクロールの間隔は異なります。

### <a name="flash-video-playback---run-for-180-seconds"></a>フラッシュ ビデオの再生 - 実行時間 180 秒以内
ユーザーは、Web ページに埋め込まれた Adobe Flash でエンコードされたビデオを表示します。 Web ページは、リモート デスクトップ セッション ホスト サーバーのローカル ハード ディスク ドライブに格納されます。 ビデオは Internet Explorer 内に埋め込まれたプレーヤー プラグインで再生されます。

このシナリオは、マルチメディアを含むリッチ コンテンツ Web ページを表示するユーザーをエミュレートします。 ほとんどのデータは VOBR を経る必要があります。

### <a name="word-remote-typing---run-for-1800-seconds"></a>Word のリモート入力 - 実行時間 1800 秒以内
ユーザーは RDP セッションでドキュメントを入力します。 RDP セッションによってクライアント側のキーボード操作は、リモート セッションで実行中の Microsoft Word のドキュメントに送信されます。 入力速度は 250 ミリ秒あたり 1 文字 (合計 7,050 文字) です。 

これは、ナレッジ ワーカーの最も一般的なシナリオの 1 つです。 このシナリオでは、現代のワーク プロセッサに入力するユーザーの応答性がテストされます。 このシナリオは、使用帯域幅の些細な変更にも反応します。

## <a name="tracking-the-test-results"></a>テスト結果の追跡
次の表を使用して、ご自分の環境内でのシナリオを評価できます。 下記のデータは単なる例であり、実際の測定値と大きく異なる場合があります。 

わかりやすいように、画面解像度 1920x1080 ピクセル、ネットワーク待機時間 (遅延) が 200 ミリ秒未満であるネットワークでの TCP トランスポート、および 120 ミリ秒+ マークのネットワーク ジッター約 1% という条件で、すべてのシナリオがテストされると仮定します。

この表について：

* **平均的なエクスペリエンス**には、ユーザーの生産性が大きな影響を受けないネットワーク帯域幅が表示されています。ビデオまたはオーディオに関する不定期の問題は除外されません。 システムは動的なロジックを利用して迅速に回復できます。 このネットワーク帯域幅の推定値は、ユーザー エクスペリエンスの質を保証することを目的としています。
  * **顕著な問題 (ブレーク ポイント)** には、ユーザーがエクスペリエンスの重大な問題に気付く可能性があり、測定可能な期間のユーザー生産性に影響が及ぶネットワーク帯域幅が表示されています。 この時点ではネットワーク帯域幅が不十分であることから、RDP アルゴリズムが正常に機能することが難しくなっており、ユーザー エクスペリエンスの質を保証できません。
  * **推奨** には、良好または非常に良好なエクスペリエンスのために推奨されるネットワーク帯域幅が表示されています。 これは通常では、対応する **平均的なエクスペリエンス** 列の値より 1 段階高くなります。
  * **メモ** には、所見とコメントが表示されています。

| テスト | 平均的なエクスペリエンス | 顕著な問題 (ブレーク ポイント) | 推奨ネットワーク帯域幅 | メモ |
| --- | --- | --- | --- | --- |
| エグゼクティブ用/複雑な PPT |10 MB/s |1 MB/s |>10 MB/s (100 MB/s 推奨) |1 MB/s で多くのアニメーションは失われます |
| 単純な PPT |5 MB/s |256 KB/s |10 MB/s |256 KB/s でスライドの読み込みがかなり遅くなります |
| Internet Explorer |10 MB/s |1 MB/s |>10 MB/s (100 MB/s 推奨) |1 MB/s で Web ビデオがぼやけて途切れ、高速なスクロールで問題が発生します |
| 単純な PDF |1 MB/s |256 KB/s |5 MB/s |256 KB/s でページの読み込みにしばらく時間がかかります |
| 混合 PDF |1 MB/s |256 KB/s |5 MB/s |256 KB/s でページの読み込みにかなりの時間がかかります |
| Flash ビデオの再生 |10 MB/s |1 MB/s |>10 MB/s (100 MB/s 推奨) |1 MB/s でビデオがざらつき、いくつかのフレームがドロップされます |
| Word のリモート入力 |256 KB/s |128 KB/s |1 MB/s |256 KB/s でユーザーがキーボード操作の間に時間がかかることに気付きます |

ユーザーごとのネットワーク帯域幅を評価するには、上記のシナリオの組み合わせと、必要なネットワーク帯域幅の対応する比率を作成します。 ご自分のシナリオに必要な最大の数値を選択します。 ユーザーが単独でシステムを使用することはほとんどないことから、同じネットワーク上で同時に作業するユーザーのための予約をご検討ください。

## <a name="learn-more"></a>詳細情報
* [Estimate Azure RemoteApp network bandwidth usage (Azure RemoteApp で使用されるネットワーク帯域幅を推定する)](remoteapp-bandwidth.md)
* [Azure RemoteApp - how do network bandwidth and quality of experience work together? (Azure RemoteApp - ネットワーク帯域幅とエクスペリエンスの質はどのような関係にあるのか)](remoteapp-bandwidthexperience.md)
* [Azure RemoteApp network bandwidth - general guidelines (if you can't test your own) (Azure RemoteApp のネットワーク帯域幅に関する一般的なガイドライン (自分でテストできない場合))](remoteapp-bandwidthguidelines.md)




<!--HONumber=Nov16_HO3-->


