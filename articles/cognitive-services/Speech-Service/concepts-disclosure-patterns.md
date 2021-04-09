---
title: 設計パターンを開示する
titleSuffix: Azure Cognitive Services
description: 開示のための設計パターンとベスト プラクティスです。
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: aec553b473e0fb2688224e8a8d027f7e9f3a6025
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716620"
---
# <a name="disclosure-design-patterns"></a>設計パターンを開示する
合成音声エクスペリエンスの適切な[開示レベル](concepts-disclosure-guidelines.md#disclosure-assessment)が決定されたので、考えられる設計パターンについて検討するよい機会です。
## <a name="overview"></a>概要
合成音声エクスペリエンスに適用できる、さまざまな開示の設計パターンがあります。 開示評価の結果が "高開示" であった場合、[**明示的開示**](#explicit-disclosure)、つまり、合成音声の出自をありのままに伝えることをお勧めします。 [**暗黙的開示**](#implicit-disclosure)には、要求される開示レベルの高低に関係なく、音声エクスペリエンスを向上させる合図と対話パターンが含まれます。
![さまざまな開示パターン](media/responsible-ai/disclosure-patterns/affordances.png)






| 明示的開示パターン                                                                                                                                                                                    | 暗黙的開示パターン                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[わかりやすい紹介](#transparent-introduction)<br> [口頭でのわかりやすい紹介](#verbal-transparent-introduction)<br>  [明示的署名](#explicit-byline)<br>  [カスタマイズと調整](#customization-and-calibration)<br> [保護者への開示](#parental-disclosure)<br> [音声の制作経緯を詳しく知る機会の提供](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [機能の開示](#capability-disclosure)<br>[暗黙的な合図とフィードバック](#implicit-cues--feedback)<br> [会話のわかりやすさ](#conversational-transparency) |



次の表を使用して、合成音声に適用されるパターンを直接参照してください。 シナリオによっては、この表の他の条件のいくつかも当てはまる場合があります。<br/>



| 合成音声エクスペリエンスが… | Recommendations | 設計パターン |
| --- | --- | --- |
| 高開示を要求する  | ユーザーが関連を想起しやすいよう、少なくとも 1 つの明示的パターンと暗黙的な合図を事前に使用します。 |[明示的開示](#explicit-disclosure)<br>[暗黙的開示](#implicit-disclosure)  |
| 低開示を要求する | 開示は最低限でよいか、不必要かもしれませんが、一部の暗黙的パターンの恩恵を受ける可能性があります。 | [機能の開示](#capability-disclosure)<br>[会話のわかりやすさ](#conversational-transparency)  |
| エンゲージメントのレベルが高い | 長期的な視点で構築し、ユーザー体験に合わせて開示するための複数のエントリ ポイントを提供します。 オンボード エクスペリエンスを設けることを強くお勧めします。 | [わかりやすい紹介](#transparent-introduction)<br>[カスタマイズと調整](#customization-and-calibration)<br>[機能の開示](#capability-disclosure) |
| 子供が主な対象ユーザーに含まれる | 主な開示対象者として保護者をターゲットにし、保護者が開示内容を子供にうまく伝えられるようにします。  | [保護者への開示](#parental-disclosure)<br>[口頭でのわかりやすい紹介](#verbal-transparent-introduction)<br> [暗黙的開示](#implicit-disclosure)<br> [会話のわかりやすさ](#conversational-transparency)  |
| 目の不自由なユーザーまたは視覚障碍のある人々が主な対象ユーザーに含まれる  | すべてのユーザーに配慮し、あらゆる形式の視覚的開示に代替テキストまたは効果音を必ず関連付けます。 コントラスト比と表示サイズについては、アクセシビリティ標準に従います。 聴覚的な合図を使用して開示内容を伝えます。  | [口頭でのわかりやすい紹介](#verbal-transparent-introduction) <br>[聴覚的な合図](#implicit-cues--feedback)<br>[触覚的な合図](#implicit-cues--feedback)<br>[会話のわかりやすさ](#conversational-transparency)<br>[アクセシビリティ標準](https://www.microsoft.com/accessibility) |
| 画面がないか、デバイスがないか、主要または唯一の対話モードとして音声を使用している | 聴覚的な合図を使用して開示内容を伝えます。 | [口頭でのわかりやすい紹介](#verbal-transparent-introduction) <br> [聴覚的な合図](#implicit-cues--feedback)  |
| 複数のユーザー/リスナーが含まれる可能性がある (例: 複数世帯のパーソナル アシスタント)  | さまざまなユーザー コンテキストと理解のレベルに留意し、ユーザー体験内で複数の開示機会を提供します。  | [わかりやすい紹介 (2 回目以降のユーザー)](#transparent-introduction)<br> [音声の制作経緯を詳しく知る機会の提供](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [会話のわかりやすさ](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>明示的開示
合成音声エクスペリエンスで高開示が要求される場合、次の明示的パターンの少なくとも 1 つを使用して、合成であることを明確に述べることが最善です。
### <a name="transparent-introduction"></a>わかりやすい紹介

音声エクスペリエンスが始まる前に、デジタル アシスタントを紹介します。このとき、デジタル アシスタントの音声の出自と、デジタル アシスタントの機能を完全に明らかにします。 このパターンを使用する最適なタイミングは、新しいユーザーをオンボードするとき、または 2 回目以降のユーザーに新しい機能を紹介するときです。 紹介の途中に暗黙的な合図を実装しておくと、デジタル エージェントでの合成音声の使用についてユーザーが心の準備をしやすくなります。

#### <a name="first-time-user-experience"></a>初回ユーザーのエクスペリエンス

![初回エクスペリエンス途中のわかりやすい紹介](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*新しいユーザーのオンボード中に合成音声が紹介されます。*

Recommendations
- 音声が人工的 (&quot;デジタル&quot;) であることを説明します
- エージェントができることを説明します
- 音声の出自を明示します
- 合成音声について詳しく知るためのエントリ ポイントを提供します

#### <a name="returning-user-experience"></a>2 回目以降のユーザーのエクスペリエンス

ユーザーがオンボード エクスペリエンスをスキップした場合は、ユーザーが最初に音声を聞くまで、わかりやすい紹介エクスペリエンスへのエントリ ポイントを提供し続けます。
<br/>

![2 回目以降のユーザー エクスペリエンス途中のわかりやすい紹介](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*合成音声エクスペリエンスへの一貫したエントリポイントを提供します。ユーザー体験のどの時点でも、ユーザーが初めて音声を聞いたときに、ユーザーがオンボード エクスペリエンスに戻れるようにします。*


### <a name="verbal-transparent-introduction"></a>口頭でのわかりやすい紹介

デジタル アシスタントの声の出自を知らせる音声プロンプトだけでも、十分に明示的であり、開示の条件を満たします。 このパターンは、音声の対話モードしか利用できない高開示シナリオに最適です。
<br/>

![口頭で読み上げられるわかりやすい紹介](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*ユーザー エクスペリエンスの中で、人の声を紹介する、または声の主を明らかにする機会があることが既にわかっている場合は、わかりやすい紹介を使用します。*


![口頭で読み上げられる一人称のわかりやすい紹介](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*わかりやすさを高めるために、合成音声の出自を声優が一人称で開示することができます。*

### <a name="explicit-byline"></a>明示的署名

このパターンは、ユーザーがオーディオ プレーヤーまたは対話型コンポーネントとやり取りして音声を聞く場合に使用します。


![ニュース メディア シナリオでの明示的署名](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*明示的署名は、音声の発信元の帰属です。*

Recommendations

- 合成音声について詳しく知るためのエントリ ポイントを提供します

### <a name="customization-and-calibration"></a>カスタマイズと調整

デジタル アシスタントがどのようにユーザーに応答するか (つまり、音声がどのように聞こえるか) をユーザーが制御できるようにします。  ユーザーが独自の用語で、特定の目標を念頭に置いてシステムと対話する場合、定義上、ユーザーはシステムが実在の人物ではないことを既に理解しています。

#### <a name="user-control"></a>ユーザー コントロール

合成音声エクスペリエンスに有意かつ顕著な影響を及ぼす選択肢を提供します。

![ユーザー設定](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*ユーザー設定は、ユーザーが自分のエクスペリエンスをカスタマイズおよび改善できるようにします。*

Recommendations

- ユーザーが音声をカスタマイズできるようにします (例: 言語や音声タイプの選択)
- 自分だけの声に応答するようシステムに指示する方法をユーザーに提供します (例: 音声調整、カスタム コマンド)
- ユーザーが生成した対話やコンテキストのある対話向けに最適化します (例: リマインダー)

#### <a name="persona-customization"></a>ペルソナのカスタマイズ

デジタル アシスタントの音声をカスタマイズする方法を提供します。 有名人や広く知られている人物が音声のベースになっている場合、ユーザーが音声をプレビューするときに視覚的な紹介と音声による紹介の両方を使用することを検討してください。

![音声のカスタマイズ](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*一連の音声から選択する機能を提供すると、人工的な性質を伝えるのに役立ちます。*

Recommendations
- ユーザーが各音声のサウンドをプレビューできるようにします
- 各音声の紹介は正確なものを使用します
- 合成音声について詳しく知るためのエントリ ポイントを提供します

### <a name="parental-disclosure"></a>保護者への開示

主な対象ユーザーが小さな子供であり、エクスポージャー レベルが高い場合は、COPPA 規制を順守することに加えて、保護者に対する開示を提供します。 センシティブな用途の場合は、合成音声の使用を成人が承認するまでエクスペリエンスを規制することを検討してください。 メッセージを子供に伝えることを保護者に促します。

![保護者向けの開示](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*保護者向けに最適化したわかりやすい紹介は、音声が合成であることを大人が事前に理解した上で、子供が音声に触れることを保証します。*

Recommendations

- 開示の主な対象者として保護者をターゲットにします
- 開示の内容を子供に伝えるよう保護者に促します
- 合成音声について詳しく知るためのエントリ ポイントを提供します
- 開示内容を読んだという、&quot;安全のための&quot;単純な質問への回答を保護者に求めることで、エクスペリエンスを規制します

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>音声の制作経緯を詳しく知る機会の提供

合成音声技術の詳細情報を提供するページ、ポップアップ、または外部サイトへの状況依存エントリ ポイントを提供します。 たとえば、オンボード中に詳細情報へのリンクを表示したり、会話中にユーザーが詳細情報を要求したときにリンクを表示したりできます。

![詳細情報のエントリ ポイント](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*合成音声の詳細を確認する機会を提供するエントリ ポイントの例。*

ユーザーが合成音声の詳細情報を要求したときの主な目標は、合成音声の出自をユーザーに示し、技術的透明性を提供することです。

![合成音声の詳細情報をユーザーに提供する](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*詳細情報は外部のヘルプ サイトで提供できます。*

Recommendations

- 複雑な概念は単純化し、法律用語や専門用語は使わないようにします
- このコンテンツが、プライバシーに関する声明や利用規約の中に埋もれないようにします
- コンテンツを簡潔に保ち、可能であれば画像を使用します

## <a name="implicit-disclosure"></a>暗黙的開示

一貫性は、ユーザー体験全体を通して暗黙的に開示を達成するための鍵です。 デバイスと対話モードの間で、視覚的な合図と聴覚的な合図を一貫して使用すれば、暗黙的パターンと明示的開示の間に関連性を構築するのに役立ちます。

![暗黙的な合図の一貫性](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>暗黙的な合図とフィードバック

擬人化は、エージェントの実際の視覚的表現から、音声、サウンド、光のパターン、跳ねる姿、さらにはデバイスの振動まで、さまざまな形で表現されます。 ペルソナを定義するときは、人間そっくりのアバターを目指すよりも、暗黙的な合図とフィードバック パターンを利用します。 これは、より明示的な開示の必要性を最小限に抑える 1 つの方法です。

![視覚的な合図とフィードバック](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*これらの合図は、人間に近づけすぎずにエージェントを擬人化するのに役立ちます。長い間一貫して使用することで、それら自体が効果的な開示メカニズムになる可能性もあります。*

次の種類の合図を組み込むときは、エクスペリエンスのさまざまな対話モードを考慮してください。

| 視覚的な合図                                                                                                                                                               | 聴覚的な合図                                                      | 触覚的な合図 |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>応答性の高いリアルタイムの合図 (例: アニメーション)<br> 画面外の合図 (例: デバイス上のライトやパターン)<br>  | ソニコン (例: 短い特徴的なサウンド、音符の連続) | 振動   |

### <a name="capability-disclosure"></a>機能の開示

デジタル アシスタントができることに関して正確な期待を設定することで、暗黙的に開示を達成できます。 サンプル コマンドを提供して、デジタル アシスタントと対話する方法をユーザーが学べるようにします。また、エクスペリエンスの早い段階から合成音声について詳しく知るための状況依存ヘルプを提供します。

![作成できる会話への既定の応答集の例。](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>会話のわかりやすさ

会話が予期しない道筋に陥った場合は、期待をリセットし、わかりやすさを高め、正しい道筋にユーザーを導くための既定の応答集を作成することを検討してください。 会話の中で明示的な開示を使用する機会もあります。

![予期しない道筋の処理](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
的外れな質問や&quot;個人的な&quot;質問がエージェントに投げかけられた場合、エージェントは人間ではないことをユーザーに思い出させて、正しい使い方をするか人間の担当者に問い合わせるよう誘導するのに最適なタイミングです。

![的外れな質問の処理](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>開示するタイミング

ユーザー体験全体を通じて、開示の機会は数多くあります。 最初の使用、2 回目の使用、n 回目の使用…を視野に設計しますが、わかりやすさを強調するために、&quot;失敗&quot;の瞬間も受け入れます。たとえば、システムがミスをしたとき、エージェントの機能の制限にユーザーが気づいたときなどです。

![ユーザー体験全体を通じた開示機会](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

さまざまな開示機会を強調する標準的なデジタル アシスタント ユーザー体験の例。

### <a name="up-front"></a>事前

開示に最適な瞬間は、ユーザーが初めて合成音声と対話するときです。  個人用音声アシスタントのシナリオでは、これはオンボード中、またはユーザーが初めてエクスペリエンスを実質的に箱から出したときです。 他のシナリオでは、それは合成音声が初めて Web サイトのコンテンツを読み上げたときかもしれませんし、ユーザーが初めて仮想のキャラクターと対話したときかもしれません。

- [わかりやすい紹介](#transparent-introduction)
- [機能の開示](#capability-disclosure)
- [カスタマイズと調整](#customization-and-calibration)
- [暗黙的な合図](#implicit-cues--feedback)

### <a name="upon-request"></a>要求時

ユーザーは、ユーザー体験の途中いつでも、要求に応じて、追加情報に容易にアクセスでき、ユーザー設定を制御でき、わかりやすい説明を受けられる必要があります。

- [音声の制作経緯を詳しく知る機会の提供](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [カスタマイズと調整](#customization-and-calibration)
- [会話のわかりやすさ](#conversational-transparency)

### <a name="continuously"></a>継続性

ユーザー エクスペリエンスを継続的に向上させる暗黙的な設計パターンを使用します。

- [機能の開示](#capability-disclosure)
- [暗黙的な合図](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>システムが失敗したとき

失敗を穏便に処理する機会として開示を使用します。

- [会話のわかりやすさ](#conversational-transparency)
- [音声の制作経緯を詳しく知る機会の提供](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [人へのハンドオフ](#conversational-transparency)

## <a name="additional-resources"></a>その他のリソース
- [Microsoft Bot ガイドライン](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana 設計ガイドライン](/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP 音声認識設計ガイドライン](/windows/uwp/design/input/speech-interactions)
- [Microsoft Windows 複合現実音声コマンド ガイドライン](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>関連項目

* [ボイス タレント向けの開示](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [合成音声テクノロジの責任あるデプロイのためのガイドライン](concepts-guidelines-responsible-deployment-synthetic.md)
* [開示する方法](concepts-disclosure-guidelines.md)