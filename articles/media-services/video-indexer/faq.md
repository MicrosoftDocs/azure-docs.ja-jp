---
title: Video Indexer についてよく寄せられる質問 - Azure
titleSuffix: Azure Media Services
description: この記事では、Azure Media Services Video Indexer についてよく寄せられる質問に対する回答を提供します。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/12/2020
ms.author: juliako
ms.openlocfilehash: 0fc28a1f808eeb2977b1dcca5046ed29933b8aa8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028796"
---
# <a name="video-indexer-frequently-asked-questions"></a>Video Indexer に関してよく寄せられる質問

この記事では、Video Indexer についてよく寄せられる質問とその回答を紹介します。

## <a name="general-questions"></a>一般的な質問

### <a name="what-is-video-indexer"></a>Video Indexer とは

Video Indexer は、Microsoft Azure Media Services に含まれている人工知能サービスです。 Video Indexer は、ビデオから詳細な分析情報を簡単に抽出するための複数の機械学習モデルのオーケストレーションを提供します。 高度で正確な分析情報を提供するために、Video Indexer はビデオの複数のチャネル (オーディオ、音声、およびビジュアル) を使用しています。 Video Indexer の分析情報は、コンテンツの見つけやすさやアクセシビリティを向上させたり、新しい収益機会を創出したり、分析情報を利用する新しい体験を構築したりするといった、さまざまな方法で使用できます。 Video Indexer は、アカウント内のモデルのテスト、構成、およびカスタマイズのための Web ベースのインターフェイスを提供します。 開発者は、REST ベースの API を使用して、Video Indexer を実稼働システムに統合することができます。 

### <a name="what-can-i-do-with-video-indexer"></a>Video Indexer を使って何ができますか?

Video Indexer がメディア ファイル上で実行できる操作の一部を次に示します。

* 音声の識別と抽出、および話者の識別を行います。
* ビデオの画面に表示されるテキストの識別と抽出を行います。
* ビデオ ファイル内のオブジェクトを検出します。
* オーディオ トラックおよびビデオの画面に表示されるテキストからブランド (Microsoft など) を識別します。
* 有名人のデータベースおよびユーザー定義の顔のデータベースから顔を検出して認識します。
* オーディオおよびビデオのコンテキスト内で話題には登場するが、特に取り上げられているわけではないトピックを抽出します。
* オーディオ トラックから字幕またはサブタイトルを作成します。

Video Indexer 機能の詳細については、[概要](video-indexer-overview.md)に関するページを参照してください。

### <a name="how-do-i-get-started-with-video-indexer"></a>Video Indexer を使い始めるにはどうすればよいですか?

Video Indexer には、Web ベースのインターフェイスなら 600 分、API 経由であれば 2,400 分ご利用いただける無料試用版が含まれています。 [Video Indexer の Web ベースのインターフェイスにログイン](https://www.videoindexer.ai/)して、Azure サブスクリプションを設定しなくても、Web ID を使用してご自分で試してみることができます。 Video Indexer の使用方法について理解を深めるには、[この簡単な概要ラボ](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)の手順に従います。

大量のビデオやオーディオ ファイルにインデックスを付けるために、Video Indexer を有料の Microsoft Azure サブスクリプションに接続することができます。 詳しい料金情報については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)に関するページをご覧ください。

詳しい開始方法については、[作業の開始](video-indexer-get-started.md)に関するページをご覧ください。

### <a name="do-i-need-coding-skills-to-use-video-indexer"></a>Video Indexer を使用するにはコーディングのスキルが必要ですか?

Video Indexer の Web ベースのインターフェイスを使用して、アカウントを評価、構成、および管理することができます。その際、**コーディングは必要ありません**。  より複雑なアプリケーションを開発する準備が整ったら、[Video Indexer API](https://api-portal.videoindexer.ai/) を使用して、独自のアプリケーション、Web サイト、または Azure Functions や [Azure Logic Apps のようなサーバーレス テクノロジを使用したカスタムのワークフロー](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)に Video Indexer を統合することができます。

### <a name="do-i-need-machine-learning-skills-to-use-video-indexer"></a>Video Indexer を使用するには機械学習のスキルが必要ですか?

いいえ、Video Indexer は、複数の機械学習モデルを 1 つのパイプラインに統合します。 Video Indexer を使用してビデオ ファイルやオーディオ ファイルのインデックスを作成すると、1 つの共有のタイムラインで抽出された分析情報の完全なセットが取得されます。お客様の方で機械学習のスキルやアルゴリズムの知識の必要はありません。

### <a name="what-media-formats-does-video-indexer-support"></a>Video Indexer はどのメディア形式をサポートしますか?

Video Indexer は、ほとんどすべての一般的なメディア形式をサポートします。 詳細については、[Azure Media Encoder Standard の形式](../latest/media-encoder-standard-formats.md)の一覧を参照してください。

### <a name="how-do-i-upload-a-media-file-into-video-indexer-and-what-are-the-limitations"></a>メディア ファイルを Video Indexer にアップロードするには、どうすればよいですか。また、どのような制限がありますか。

Video Indexer の Web ベース ポータルでは、ファイルのアップロード ダイアログを使用するか、ソース ファイルを直接ホストする URL を指定して、メディア ファイルをアップロードできます ([例](https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4)を参照)。 iFrame や埋め込みコードを使用しているメディア コンテンツをホストする URL は、動作しません ([例](https://www.videoindexer.ai/accounts/7e1282e8-083c-46ab-8c20-84cae3dc289d/videos/5cfa29e152/?t=4.11)を参照)。 

詳細については、この[攻略ガイド](./upload-index-videos.md)を参照してください。

#### <a name="limitations"></a>制限事項

* ビデオの名前は、80 文字以内にする必要があります。
* バイト配列を使用してビデオをアップロードする場合、ビデオのサイズは 2 GB に制限されます (URL を使用する場合は 30 GB)。 

包括的な一覧については、「[アップロードに関する考慮事項と制限事項](upload-index-videos.md#uploading-considerations-and-limitations)」を参照してください。

### <a name="how-long-does-it-take-video-indexer-to-extract-insights-from-media"></a>Video Indexer がメディアから分析情報を抽出するには、どの程度の時間がかかりますか?

ビデオまたはオーディオ ファイルにインデックスを付けるために要する時間は、Video Indexer API または Video Indexer の Web ベース インターフェイスのどちらを使用する場合も、複数のパラメーターによって決まります。パラメーターには、ファイルの長さと品質、ファイルから見つかる分析情報の数、使用可能な[予約ユニット](../previous/media-services-scale-media-processing-overview.md)の数、[ストリーミング エンドポイント](../previous/media-services-streaming-endpoints-overview.md)が有効かどうかなどがあります。 予想を確かなものにするには、独自のコンテンツを含むいくつかのテスト ファイルを実行して平均を取ることをお勧めします。

### <a name="can-i-create-customized-workflows-to-automate-processes-with-video-indexer"></a>Video Indexer でプロセスを自動化するカスタマイズされたワークフローを作成できますか?

はい、Video Indexer を Logic Apps、Flow、[Azure Functions](https://azure.microsoft.com/services/functions/) などのサーバーレス テクノロジに統合できます。 [Logic Apps](https://azure.microsoft.com/services/logic-apps/) と [Flow](https://flow.microsoft.com/en-us/) の Video Indexer 用のコネクタについては、[こちら](https://azure.microsoft.com/blog/logic-apps-flow-connectors-will-make-automating-video-indexer-simpler-than-ever/)をご覧ください。 パートナーによって実行される一部のオートメーション プロジェクトは、[Video Indexer サンプル](https://github.com/Azure-Samples/media-services-video-indexer) リポジトリで見ることができます。

### <a name="in-which-azure-regions-is-video-indexer-available"></a>Video indexer はどの Azure リージョンで利用できますか?

Video Indexer を利用できる Azure リージョンについては、[リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)のページをご覧ください。

### <a name="can-i-customize-video-indexer-models-for-my-specific-use-case"></a>特定のユース ケースに合わせて Video Indexer モデルをカスタマイズできますか。 

はい。 Video Indexer では、使用可能なモデルをニーズに合わせてカスタマイズできます。 

たとえば、Person モデルでは、1,000,000 人の有名人の顔がすぐに認識されますが、そのデータベースにはない他の顔を認識するようにトレーニングすることもできます。 

詳細については、[Person](customize-person-model-overview.md)、[Brands](customize-brands-model-overview.md)、[Language](customize-language-model-overview.md) モデルのカスタマイズに関する記事を参照してください。 

###  <a name="can-i-edit-the-videos-in-my-library"></a>ライブラリ内のビデオを編集できますか。

はい。 ライブラリの表示で **[ビデオの編集]** ボタンを押すか、プレーヤーの表示で **[エディターで開く]** ボタンをクリックして **[プロジェクト]** タブに移動します。新しいプロジェクトを作成し、ライブラリからさらにビデオを追加して、それらをまとめて編集できます。完了したら、ビデオをレンダリングしてダウンロードすることができます。 

新しいビデオについての分析情報を得るには、Video Indexer でインデックスを付けます。ビデオとその分析情報がライブラリに表示されるようになります。

### <a name="can-i-index-multiple-audio-streams-or-channels"></a>複数のオーディオ ストリームまたはチャネルにインデックスを付けることができますか。

複数のオーディオ ストリームがある場合、Video Indexer では最初に検出されたストリームだけが処理されます。 Video Indexer で処理されるオーディオ ストリームでは、さまざまなチャネル (存在する場合) が取得され、モノとしてまとめて処理されます。 ストリーム/チャネルの操作では、インデックスを付ける前に、ファイルに ffmpeg コマンドを使用できます。

### <a name="what-is-the-sla-for-video-indexer"></a>Video Indexer の SLA はどうなっていますか?

Video Indexer については Azure Media Service の SLA が適用されます。[SLA](https://azure.microsoft.com/support/legal/sla/media-services/v1_2/) に関するページを参照してください。 SLA は Video Indexer の有料アカウントのみに適用され、無料試用版には適用されません。

## <a name="privacy-questions"></a>プライバシーに関する質問

### <a name="are-video-and-audio-files-indexed-by-video-indexer-stored"></a>Video Indexer によってインデックスが付けられるビデオ ファイルやオーディオ ファイルは保存されますか?

はい、Video Indexer の Web サイトまたは API を使用して Video Indexer からファイルを削除しない限り、ビデオ ファイルとオーディオ ファイルは保存されます。 無料試用版では、インデックスを付けたビデオ ファイルおよびオーディオ ファイルは、米国東部の Azure リージョンに保存されます。 それ以外の場合、ビデオ ファイルとオーディオ ファイルは Azure サブスクリプションのストレージ アカウントに保存されます。

### <a name="can-i-delete-my-files-that-are-stored-in-video-indexer-portal"></a>Video Indexer Portal に保存されているファイルを削除できますか?

はい、ビデオ ファイルやオーディオ ファイルだけでなく、Video Indexer によってそれらのファイルから抽出されたメタデータや分析情報も削除することができます。 Video Indexer からファイルを削除すると、そのファイルとそのメタデータおよび分析情報は Video Indexer から完全に削除されます。 ただし、Azure ストレージに独自のバックアップ ソリューションを実装している場合、ファイルは Azure ストレージに残ります。

### <a name="can-i-control-user-access-to-my-video-indexer-account"></a>Video Indexer アカウントのユーザー アクセスを制御できますか?

はい、アカウント管理者だけが、自分のアカウントにユーザーを招待したりメンバーを削除したりすることができ、編集特権を持つユーザーと読み取り専用アクセスを持つユーザーを割り当てることもできます。

### <a name="who-has-access-to-my-video-and-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Video Indexer でインデックスを作成したり保存したりしたビデオ ファイルやオーディオ ファイルと、抽出されたメタデータと分析情報には、誰がアクセスできますか?

ビデオのプライバシー設定がパブリックになっているビデオやオーディオのコンテンツには、ビデオやオーディオのコンテンツとその分析情報へのリンクを持つすべてのユーザーがアクセスできます。 ビデオのプライバシー設定がプライベートになっているビデオやオーディオのコンテンツには、ビデオやオーディオのコンテンツのアカウントに招待されたユーザーのみがアクセスできます。 コンテンツのプライバシーの設定は、Video Indexer で抽出されるメタデータと分析情報にも適用されます。 プライバシーの設定は、ビデオ ファイルやオーディオ ファイルをアップロードするときに割り当てます。 また、インデックスの作成後にプライバシーの設定を変更することもできます。

### <a name="what-access-does-microsoft-have-to-my-video-or-audio-files-that-have-been-indexed-andor-stored-by-video-indexer-and-the-metadata-and-insights-that-were-extracted"></a>Video Indexer で索引付けまたは保存されたビデオやオーディオ ファイル、および抽出されたメタデータと分析情報にアクセスできるのは誰ですか?

[Azure オンライン サービス条件](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) (OST) に従い、お客様はご自分のコンテンツを完全に所有し、Microsoft は、お客様のコンテンツと、OST と Microsoft のプライバシーに関する声明に従って Video Indexer でお客様のコンテンツから抽出するメタデータと分析情報のみにアクセスします。

### <a name="are-the-custom-models-that-i-build-in-my-video-indexer-account-available-to-other-accounts"></a>自分の Video Indexer アカウントで作成したカスタム モデルを他のアカウントで使用できますか?

 いいえ、自分のアカウントで作成したカスタム モデルは、他のアカウントでは使用できません。 Video Indexer では、現在、自分のアカウントで[ブランド](customize-brands-model-overview.md)、[言語](customize-language-model-overview.md)、および[人](customize-person-model-overview.md)のカスタム モデルを作成できます。 これらのモデルは、モデルを作成したアカウントでのみ使用できます。
  
### <a name="is-the-content-indexed-by-video-indexer-kept-within-the-azure-region-where-i-am-using-video-indexer"></a>Video Indexer によってインデックスが付けられたコンテンツは、使用中の Video Indexer がある Azure リージョン内に保持されますか?

はい、コンテンツとその分析情報は、その Azure リージョン内に保持されますが、複数の Azure リージョンを使用するように Azure サブスクリプションを手動構成した場合を除きます。 

### <a name="what-is-the-privacy-policy-for-video-indexer"></a>Video Indexer のプライバシー ポリシーとは何ですか。

Video Indexer には [Microsoft のプライバシーに関する声明](https://privacy.microsoft.com/privacystatement)が適用されます。 プライバシーに関する声明では、Microsoft が処理する個人データ、Microsoft がそれをどのように処理し、何の目的でそれを処理するかについて説明しています。 プライバシーについて詳しくは、[Microsoft Trust Center](https://www.microsoft.com/trustcenter) を参照してください。

### <a name="what-certifications-does-video-indexer-have"></a>Video Indexer には、どのような認証がありますか?

Video Indexer は、現在のところ SOC 認証を取得しています。 Video Indexer の認証を確認するには、[Microsoft Trust Center](https://www.microsoft.com/trustcenter/compliance/complianceofferings?product=Azure) を参照してください。

### <a name="what-is-the-difference-between-private-and-public-videos"></a>プライベート ビデオとパブリック ビデオの違いは何ですか。 

Video Indexer にビデオをアップロードするとき、プライベートとパブリックという 2 つのプライバシー設定から選択できます。 パブリック ビデオには、匿名ユーザーや身元が不明のユーザーを含め、誰でもアクセスできます。 プライベート ビデオは、アカウント メンバーにのみ限定されます。 

### <a name="i-tried-to-upload-a-video-as-public-and-it-was-flagged-for-inappropriate-or-offensive-content-what-does-that-mean"></a>パブリックとしてビデオをアップロードしたら、不適切または不快なコンテンツのフラグが付けられました。これは何を意味しますか。 

Video Indexer にビデオがアップロードされると、不適切なコンテンツが一般公開されないよう、アルゴリズムとモデルによってコンテンツが自動的に分析されます。 過激な描写のコンテンツが含まれるものとビデオが疑われた場合、パブリックとして設定することができなくなります。 ただし、アカウント メンバーであれば、プライベート ビデオとしてアクセスできます (表示したり、分析情報や抽出された成果物をダウンロードしたり、アカウント メンバーが利用できるその他の操作を実行したりできます)。   

パブリック アクセス用にビデオを設定するには、以下のいずれかを実行します。 

* 独自のインターフェイス レイヤー (アプリや Web サイトなど) を構築し、それを利用して Video Indexer サービスを操作します。 この方法では、ビデオは Microsoft のポータル内でプライベートになり、ユーザーはインターフェイス経由でビデオを操作できます。 たとえば、独自のインターフェイスで分析情報を取得したり、ビデオの視聴を許可したりできます。 
* コンテンツの人間によるレビューを要求します。過激な描写のコンテンツでなければ、制限が解除されることがあります。 

    ユーザーがインターフェイス レイヤーとして、かつパブリック (認証なし) の視聴目的で Video Indexer Web サイトを直接使用する場合、このオプションを試すことができます。 

## <a name="api-questions"></a>API に関する質問

### <a name="what-apis-does-video-indexer-offer"></a>Video Indexer ではどのような API が提供されていますか?

Video Indexer の API では、インデックス作成、メタデータの抽出、アセット管理、翻訳、埋め込み、モデルのカスタマイズなどができます。 Video Indexer API の使用方法の詳細については、[Video Indexer 開発者ポータル](https://api-portal.videoindexer.ai/)を参照してください。

### <a name="what-client-sdks-does-video-indexer-offer"></a>Video Indexer ではどのようなクライアント SDK が提供されていますか?

現在のところ、クライアント SDK は提供されていません。 Video Indexer チームは SDK に取り組んでいる最中で、まもなく提供開始の予定です。

### <a name="how-do-i-get-started-with-video-indexers-api"></a>Video Indexer API の使用を開始するにはどうすればよいですか?

[チュートリアル: Video Indexer API の使用を開始する](video-indexer-use-apis.md)に従ってください。

### <a name="what-is-the-difference-between-the-video-indexer-api-and-the-azure-media-service-v3-api"></a>Video Indexer API と Azure Media Service v3 API の違いは何ですか?

現在、Video Indexer API と Azure Media Services v3 API によって提供される機能には重複があります。 両方のサービスを比較する方法の詳細については、[こちら](compare-video-indexer-with-media-services-presets.md)をご覧ください。

### <a name="what-is-an-api-access-token-and-why-do-i-need-it"></a>API アクセス トークンとは何で、なぜ必要なのでしょうか?

Video Indexer API には Authorization API (承認 API) と Operations API (操作 API) が含まれています。 Authorizations API に含まれている呼び出しによって、アクセス トークンがユーザーに付与されます。 Operations API の各呼び出しは、呼び出しの承認スコープと一致するアクセス トークンに関連付けられている必要があります。

アクセス トークンが必要なのは、Video Indexer API をセキュリティ目的で使用する場合です。 この情報により、すべての呼び出しが、ユーザーまたはユーザーのアカウントへのアクセス許可を持つ人物から送信されたものであることが保証されます。 

### <a name="what-is-the-difference-between-account-access-token-user-access-token-and-video-access-token"></a>アカウント アクセス トークン、ユーザー アクセス トークン、ビデオ アクセス トークンはどう違うのですか?

* アカウント レベル - アカウント レベルのアクセス トークンを使用すると、アカウント レベルまたはビデオ レベルに対して操作を実行できます。 たとえば、動画のアップロード、すべてのビデオの一覧表示、動画の分析情報の取得です。
* ユーザー レベル - ユーザー レベルのアクセス トークンを使用すると、ユーザー レベルに対して操作を実行できます。 たとえば、関連付けられたアカウントの取得などです。
* ビデオ レベル - ビデオ レベルのアクセス トークンを使用すると、特定のビデオに対して操作を実行できます。 たとえば、ビデオの分析情報、キャプションのダウンロード、ウィジェットの取得などです。

### <a name="how-often-do-i-need-to-get-a-new-access-token-when-do-access-tokens-expire"></a>どのくらいの頻度で新しいアクセス トークンを取得する必要がありますか。 アクセス トークンの有効期限はいつ切れますか?

アクセス トークンは 1 時間ごとに期限が切れるため、1 時間ごとに新しいアクセス トークンを生成する必要があります。 

### <a name="what-are-the-login-options-to-video-indexer-developer-portal"></a>Video Indexer の開発者ポータルにはどのようなログイン オプションがありますか?

[ログイン情報](release-notes.md#october-2020)に関するリリース ノートを参照してください。

ID プロバイダーを使用して電子メール アカウントを登録した後は、この電子メール アカウントを別の ID プロバイダーで使用することはできません。

## <a name="billing-questions"></a>課金に関する質問

### <a name="how-much-does-video-indexer-cost"></a>Video Indexer のコストはどれくらいですか?

Video Indexer では、インデックスを作成するコンテンツの入力期間に基づいた、単純な従量課金制の価格モデルを使用しています。 エンコード、ストリーミング、ストレージ、ネットワークの使用状況、およびメディア占有ユニットについては追加料金が適用されることがあります。 詳細については、[価格](https://azure.microsoft.com/pricing/details/cognitive-services/video-indexer/)に関するページを参照してください。

### <a name="when-am-i-billed-for-using-video-indexer"></a>Video Indexer の使用料はいつ請求されますか。

インデックス付きのためビデオを送信した場合、ユーザーはインデックスをビデオ分析、オーディオ分析、またはその両方として定義します。 これにより、課金される SKU が決まります。 処理中に重大レベルのエラーが発生した場合、エラー コードが応答として返されます。 こうした場合、課金は行われません。  重大なエラーは、コード内のバグか、サービスが持つ内部的異存の重大なエラーによって発生します。 間違った認識や分析情報の抽出などのエラーは重大と見なされず、応答が返されます。 有効な (エラーのないコード) 応答が返された場合はすべて、課金が行われます。
 
### <a name="does-video-indexer-offer-a-free-trial"></a>Video Indexer では無料試用版が提供されていますか?

はい、Video Indexer には、完全なサービスおよび API 機能を提供する無料試用版が用意されています。 Web ベースのインターフェイスのユーザーについてはビデオ 600 分、API ユーザーについては 2,400 分のクォータがあります。 

## <a name="next-steps"></a>次のステップ

* [概要](video-indexer-overview.md)
* [Stack Overflow](https://stackoverflow.com/search?q=video-indexer)
