---
title: トラブルシューティング - QnA Maker
description: QnA Maker サービスについてきわめて多く寄せられる質問を精選した一覧です。サービスの採用にかかる期間を短縮し、成果を上げるためにお役立てください。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: troubleshooting
ms.date: 11/09/2020
ms.openlocfilehash: 7b7ac20672ee653cbf6d2b82b7a9454c1d742b2c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102612696"
---
# <a name="troubleshooting-for-qna-maker"></a>QnA Maker のトラブルシューティング

QnA Maker サービスについてきわめて多く寄せられる質問を精選した一覧です。サービスの採用にかかる期間を短縮し、成果を上げるためにお役立てください。

<a name="how-to-get-the-qnamaker-service-hostname"></a>

## <a name="manage-predictions"></a>予測を管理する

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

<details>
<summary><b>クエリ予測のスループットのパフォーマンスを向上させるにはどうすればよいですか。</b></summary>

**回答**: スループットのパフォーマンスの問題は、App Service と Cognitive Search の両方をスケールアップする必要があることを示しています。 パフォーマンスを向上させるには、Cognitive Search にレプリカを追加することを検討してください。

価格レベルの詳細については[こちら](Concepts/azure-resources.md)をご覧ください。
</details>

<details>
<summary><b>QnA Maker サービス エンドポイントを取得する方法</b></summary>

**回答**: QnA Maker サービス エンドポイントは、デバッグ目的で QnA Maker サポートや UserVoice に問い合わせる場合に役立ちます。 エンドポイントは、次の形式の URL です: `https://your-resource-name.azurewebsites.net`。

1. [Azure portal](https://portal.azure.com) で QnA Maker サービス (リソース グループ) に移動します。

    ![QnAMaker の Azure リソース グループ (Azure portal)](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. QnA Maker リソースに関連付けられている App Service を選択します。 通常、名前は同じです。

     ![QnAMaker の App Service を選択する](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. エンドポイントの URL は [概要] セクションで確認できます。

    ![QnA Maker のエンドポイント](./media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

<details>
<summary><b>クエリ予測のスループットのパフォーマンスを向上させるにはどうすればよいですか。</b></summary>

**回答**: スループットのパフォーマンスの問題は、Cognitive Search をスケールアップする必要があることを示しています。 パフォーマンスを向上させるには、Cognitive Search にレプリカを追加することを検討してください。

価格レベルの詳細については[こちら](Concepts/azure-resources.md)をご覧ください。
</details>

---

## <a name="manage-the-knowledge-base"></a>ナレッジ ベースの管理

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

<details>
<summary><b>自分の QnA Maker の一部を誤って削除しました。どうしたらいいでしょうか。</b></summary>

**回答**: Search や Web App など、QnA Maker リソースと一緒に作成される Azure サービスは削除しないでください。 QnA Maker が正常に機能するために必要であり、削除すると QnA Maker が正しく動作しなくなります。

質問と回答のペア、ファイル、URL、カスタムの質問と回答、ナレッジ ベース、Azure リソースを含め、削除はすべて永続的です。 ナレッジ ベースの一部を削除する前に、 **[設定]** ページから確実にナレッジ ベースをエクスポートしてください。

</details>

<details>
<summary><b>指定した URL/ファイルから質問とその回答のペアが抽出されません。なぜでしょうか。</b></summary>

**回答**: QnA Maker は、有効な FAQ URL であっても、一部の Q&A (QnA) コンテンツを自動抽出できない場合があります。 そのような場合は、QnA コンテンツを .txt ファイルに貼り付けて、ツールによる取り込みが可能かどうかを確認できます。 または、[QnA Maker ポータル](https://qnamaker.ai)経由でナレッジ ベースに編集という形でコンテンツを追加することもできます。

</details>

<details>
<summary><b>作成できるナレッジ ベースの最大サイズはどれくらいですか。</b></summary>

**回答**: ナレッジ ベースのサイズは、QnA Maker サービスの作成時に選択した Azure Search の SKU により異なります。 詳細については、[こちら](./concepts/azure-resources.md)を参照してください。

</details>

<details>
<summary><b>新しいナレッジ ベースを作成しようとしたときに、ドロップダウンに選択肢が表示されません。なぜでしょうか?</b></summary>

**回答**: まだ Azure に QnA Maker サービスが作成されていません。 その方法については、[こちら](./How-To/set-up-qnamaker-service-azure.md)を参照してください。

</details>

<details>
<summary><b>複数人でナレッジ ベースを共有するにはどうすればよいですか?</b></summary>

**回答**: 共有は、QnA Maker サービス レベルで行われます。つまり、サービス内のすべてのナレッジ ベースが共有されます。 ナレッジ ベースの共同作業の方法については、[こちら](./index.yml)を参照してください。

</details>

<details>
<summary><b>ナレッジ ベースを変更するために、同じ AAD テナントに含まれない共同作成者とナレッジ ベースを共有できますか。</b></summary>

**回答**: 共有は、Azure ロールベースのアクセス制御 (Azure RBAC) に基づきます。 Azure で他のユーザーと _任意の_ リソースを共有できる場合、QnA Maker を共有することもできます。

</details>

<details>
<summary><b>5 つの QnAMaker ナレッジ ベースがある 1 つの App Service プランがある場合、5 人のユーザーに、それぞれが 1 つだけの QnAMaker ナレッジ ベースにアクセスできるように、読み取り/書き込み権限を割り当てることができますか。</b></summary>

**回答**: 個々のナレッジ ベースではなく、QnAMaker サービス全体を共有できます。

</details>

<details>
<summary><b>適切な一致が見つからなかったときの既定のメッセージを変更するにはどうすればよいですか?</b></summary>

**回答**: 既定のメッセージは、App Service の設定に含まれています。
- Azure portal の App Service リソースに移動します

![QnA Maker App Service](./media/qnamaker-faq/qnamaker-resource-list-appservice.png)
- **[設定]** オプションをクリックします。

![QnA Maker App Service の設定](./media/qnamaker-faq/qnamaker-appservice-settings.png)
- **[DefaultAnswer]** という設定の値を変更します。
- App Service を再起動します。

![QnA Maker App Service の再起動](./media/qnamaker-faq/qnamaker-appservice-restart.png)


</details>

<details>
<summary><b>SharePoint リンクが抽出されません。なぜでしょうか?</b></summary>

**回答**: 詳細については、「[Data source locations (データ ソースの場所)](./concepts/data-sources-and-content.md#data-source-locations)」を参照してください。

</details>

<details>
<summary><b>ナレッジ ベースに対して行った更新が発行時に反映されません。なぜ表示されないのですか?</b>"</summary>

**回答**: 編集内容を公開するには、テーブルの更新、テスト、設定のいずれであってもすべて保存する必要があります。 編集操作後は、必ず **[Save and train]\(保存してトレーニング\)** ボタンをクリックしてください。

</details>

<details>
<summary><b>ナレッジ ベースは、リッチ データやマルチメディアに対応していますか?</b></summary>

**回答**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>ファイルと URL のマルチメディア自動抽出

* URL - HTML から Markdown への変換機能は制限されています。
* ファイル - サポートされていません

#### <a name="answer-text-in-markdown"></a>マークダウンの回答テキスト
QnA ペアをナレッジ ベースに追加すると、回答のマークダウン テキストを編集して、パブリック URL から使用できるメディアへのリンクを含めることができます。


</details>

<details>
<summary><b>QnA Maker では英語以外の言語もサポートされますか。</b></summary>

**回答**: 詳細については、[サポートされている言語](./overview/language-support.md)に関するページを参照してください。

多言語コンテンツが含まれる場合は、言語ごとにサービスを作成してください。

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

<details>
<summary><b>指定した URL/ファイルから質問とその回答のペアが抽出されません。なぜでしょうか。</b></summary>

**回答**: QnA Maker は、有効な FAQ URL であっても、一部の Q&A (QnA) コンテンツを自動抽出できない場合があります。 そのような場合は、QnA コンテンツを .txt ファイルに貼り付けて、ツールによる取り込みが可能かどうかを確認できます。 または、[QnA Maker ポータル](https://qnamaker.ai)経由でナレッジ ベースに編集という形でコンテンツを追加することもできます。

</details>

<details>
<summary><b>作成できるナレッジ ベースの最大サイズはどれくらいですか。</b></summary>

**回答**: ナレッジ ベースのサイズは、QnA Maker サービスの作成時に選択した Azure Search の SKU により異なります。 詳細については、[こちら](./concepts/azure-resources.md)を参照してください。

</details>

<details>
<summary><b>新しいナレッジ ベースを作成しようとしたときに、ドロップダウンに選択肢が表示されません。なぜでしょうか?</b></summary>

**回答**: まだ Azure に QnA Maker サービスが作成されていません。 その方法については、[こちら](./How-To/set-up-qnamaker-service-azure.md)を参照してください。

</details>

<details>
<summary><b>複数人でナレッジ ベースを共有するにはどうすればよいですか?</b></summary>

**回答**: 共有は、QnA Maker サービス レベルで行われます。つまり、サービス内のすべてのナレッジ ベースが共有されます。 ナレッジ ベースの共同作業の方法については、[こちら](./index.yml)を参照してください。

</details>

<details>
<summary><b>ナレッジ ベースを変更するために、同じ Azure Active Directory テナントに含まれない共同作成者とナレッジ ベースを共有できますか。</b></summary>

**回答**: 共有は、Azure ロールベースのアクセス制御 (Azure RBAC) に基づきます。 Azure で他のユーザーと _任意の_ リソースを共有できる場合、QnA Maker を共有することもできます。

</details>

<details>
<summary><b>5 人のユーザーに、それぞれが 1 つだけの QnAMaker ナレッジ ベースにアクセスできるように、読み取り/書き込み権限を割り当てることができますか。</b></summary>

**回答**: 個々のナレッジ ベースではなく、QnAMaker サービス全体を共有できます。

</details>

<details>
<summary><b>SharePoint リンクが抽出されません。なぜでしょうか?</b></summary>

**回答**: 詳細については、「[Data source locations (データ ソースの場所)](./concepts/data-sources-and-content.md#data-source-locations)」を参照してください。

</details>

<details>
<summary><b>ナレッジ ベースに対して行った更新が発行時に反映されません。なぜ表示されないのですか?</b>"</summary>

**回答**: 編集内容を公開するには、テーブルの更新、テスト、設定のいずれであってもすべて保存する必要があります。 編集操作後は、必ず **[Save and train]\(保存してトレーニング\)** ボタンをクリックしてください。

</details>

<details>
<summary><b>ナレッジ ベースは、リッチ データやマルチメディアに対応していますか?</b></summary>

**回答**:

#### <a name="multimedia-auto-extraction-for-files-and-urls"></a>ファイルと URL のマルチメディア自動抽出

* URL - HTML から Markdown への変換機能は制限されています。
* ファイル - サポートされていません

#### <a name="answer-text-in-markdown"></a>マークダウンの回答テキスト
QnA ペアをナレッジ ベースに追加すると、回答のマークダウン テキストを編集して、パブリック URL から使用できるメディアへのリンクを含めることができます。


</details>

<details>
<summary><b>QnA Maker では英語以外の言語もサポートされますか。</b></summary>

**回答**: 詳細については、[サポートされている言語](./overview/language-support.md)に関するページを参照してください。

多言語コンテンツが含まれる場合は、言語ごとにサービスを作成してください。

</details>

---

## <a name="manage-service"></a>サービスの管理

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

<details>
<summary><b>いつアプリ サービスを再起動するべきですか。</b></summary>

**回答**: **[ユーザー設定]** [ページ](https://www.qnamaker.ai/UserSettings)の **[エンドポイント キー]** で、ナレッジ ベースのバージョン値の隣に注意のアイコンが表示されているときは、アプリ サービスを更新してください。

</details>

<details>
<summary><b>既存の Search Service を削除してしまいました。どうしたらいいですか。</b></summary>

**回答**: Azure Cognitive Search インデックスを削除した場合、この操作は最終的なものであり、インデックスを復旧することはできません。

</details>

<details>
<summary><b>Search Service の `testkb` インデックスを削除してしまいました。どうしたらいいですか。</b></summary>

**回答**: 古いデータを回復することはできません。 新しい QnA Maker リソースを作成し、ナレッジ ベースを再度作成してください。

</details>

<details>
<summary><b>エンドポイント キーは、どのようなときに更新すべきでしょうか?</b></summary>

**回答**: エンドポイント キーの更新は、鍵漏えいのおそれがある場合に行ってください。

</details>

<details>
<summary><b>複数の言語を使用したナレッジ ベースに同じ Azure Cognitive Search リソースを使用できますか?</b></summary>

**回答**: 複数の言語と複数のナレッジ ベースを使用するには、言語ごとに QnA Maker リソースを作成する必要があります。 これにより、言語ごとに個別の Azure Search サービスが作成されます。 1 つの Azure Search サービスに異なる言語のナレッジ ベースを混在させると、結果の関連性が低下します。

</details>

<details>
<summary><b>QnA Maker で使用される Azure Cognitive Search リソースの名前を変更するには、どうすればよいですか?</b></summary>

**回答**: Azure Cognitive Search リソースの名前は、QnA Maker リソース名の後にいくつかのランダムな文字が追加されたものです。 そのため、QnA Maker の複数の Search リソースを識別することが難しくなります。 別の検索サービスを作成し (任意の方法で名前を付け)、それを QnA サービスに接続します。 手順は、[Azure Search をアップグレードする](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)ために必要な手順と同様です。

</details>

<details>
<summary><b>QnA Maker から `Runtime core is not initialized,` が返された場合は、どのように解決すればよいですか?</b></summary>

**回答**: App Service のディスク領域がいっぱいになっている可能性があります。 ディスク領域を修正する手順は次のとおりです。

1. [Azure portal](https://portal.azure.com) で、QnA Maker の App Service を選択し、サービスを停止します。
1. この App Service で、 **[開発ツール]** 、 **[高度なツール]** 、 **[移動]** の順に選択します。 これで新しいブラウザー ウィンドウが開きます。
1. **[デバッグ コンソール]** 、 **[CMD]** の順に選択してコマンドライン ツールを開きます。
1. _site/wwwroot/Data/QnAMaker/_ ディレクトリに移動します。
1. 名前が `rd` で始まるフォルダーをすべて削除します。

    次の項目は **削除しないでください**。

    * KbIdToRankerMappings.txt ファイル
    * EndpointSettings.json ファイル
    * EndpointKeys フォルダー

1. App Service を開始します。
1. ナレッジ ベースにアクセスして動作していることを確認します。

</details>
<details>
<summary><b>Application Insights が機能しないのはなぜですか?</b></summary>

**回答**: 問題を解決するには、次の手順を照合確認して更新してください。

1. App Service -> [設定] グループ -> [構成] セクション -> [アプリケーション設定] で、[名前] の "UserAppInsightsKey" パラメーターが適切に構成され、それぞれの Application Insights の [概要] タブ ("インストルメンテーション キー") Guid に設定されています。 

1. App Service -> [設定] グループ -> [Application Insights] セクションで、Application Insights が有効になっていて、それぞれの Application Insights リソースに接続されていることを確認します。

</details>

<details>
<summary><b>Application Insights が有効になっていますが、正常に動作していないのはなぜですか?</b></summary>

**回答**: 次の手順に従ってください。 

1.  '"APPINSIGHTS_INSTRUMENTATIONKEY" name' の値を 'UserAppInsightsKey' name にコピーします。既に値が存在する場合は、オーバーライドします。 

1.  'UserAppInsightsKey' キーがアプリの設定に存在しない場合は、その名前の新しいキーを追加し、値をコピーします。

1.  保存すると、App Service が自動的に再起動します。 これで問題が解決します。 

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)


<details>
<summary><b>既存の Search Service を削除してしまいました。どうしたらいいですか。</b></summary>

**回答**: Azure Cognitive Search インデックスを削除した場合、この操作は最終的なものであり、インデックスを復旧することはできません。

</details>

<details>
<summary><b>Search Service の `testkb` インデックスを削除してしまいました。どうしたらいいですか。</b></summary>

**回答**: 古いデータを回復することはできません。 新しい QnA Maker リソースを作成し、ナレッジ ベースを再度作成してください。

</details>

<details>
<summary><b>複数の言語を使用したナレッジ ベースに同じ Azure Cognitive Search リソースを使用できますか?</b></summary>

**回答**: 複数の言語と複数のナレッジ ベースを使用するには、言語ごとに QnA Maker リソースを作成する必要があります。 これにより、言語ごとに個別の Azure Search サービスが作成されます。 1 つの Azure Search サービスに異なる言語のナレッジ ベースを混在させると、結果の関連性が低下します。

</details>

<details>
<summary><b>QnA Maker で使用される Azure Cognitive Search リソースの名前を変更するには、どうすればよいですか?</b></summary>

**回答**: Azure Cognitive Search リソースの名前は、QnA Maker リソース名の後にいくつかのランダムな文字が追加されたものです。 そのため、QnA Maker の複数の Search リソースを識別することが難しくなります。 別の検索サービスを作成し (任意の方法で名前を付け)、それを QnA サービスに接続します。 手順は、[Azure Search をアップグレードする](How-To/set-up-qnamaker-service-azure.md#upgrade-the-azure-cognitive-search-service)ために必要な手順と同様です。

</details>

---

## <a name="integrate-with-other-services-including-bots"></a>ボットなどの他のサービスとの統合

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

<details>
<summary><b>QnA Maker を利用するには Bot Framework を使う必要がありますか?</b></summary>

**回答**: いいえ。QnA Maker に関して [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) を使用する必要はありません。 ただし、QnA Maker は [Azure Bot Service](/azure/bot-service/) に用意されているいくつかのテンプレートの 1 つとして提供されています。 Bot Service では、Microsoft Bot Framework を使用してインテリジェントなボットを迅速に開発し、サーバーレス環境で実行することができます。

</details>

<details>
<summary><b>QnA Maker で新しいボットを作成するにはどうすればよいですか?</b></summary>

**回答**: Azure Bot Service でボットを作成するには、[こちら](./Quickstarts/create-publish-knowledge-base.md)のドキュメントの手順に従ってください。

</details>

<details>
<summary><b>既存の Azure Bot Service で別のナレッジ ベースを使用するにはどうすればよいですか?</b></summary>

**回答**: 次のナレッジ ベースに関する情報が必要です。

* ナレッジ ベース ID。
* ナレッジ ベースの公開されたエンドポイント カスタム サブドメイン名 (`host` と呼ばれます)。公開後に **[設定]** ページで見つかります。
* ナレッジ ベースの公開されたエンドポイント キー - 公開後に **[設定]** ページで見つかります。

この情報を使用して、Azure portal でご利用のボットのアプリ サービスにアクセスします。 **[設定] -> [構成] -> [アプリケーション設定]** で、これらの値を変更します。

ナレッジ ベースのエンドポイント キーは、ABS サービスで `QnAAuthkey` としてラベルが付けられます。

</details>

<details>
<summary><b>2 つ以上のクライアント アプリケーションでナレッジ ベースを共有できますか?</b></summary>

**回答**: はい。ナレッジ ベースには、任意の数のクライアントからクエリを実行できます。 ナレッジ ベースからの応答が低速であるか、タイムアウトになっている場合は、ナレッジ ベースに関連付けられているアプリ サービスのサービス レベルをアップグレードすることを検討してください。

</details>

<details>
<summary><b>QnA Maker サービスを Web サイトに埋め込むにはどうすればよいですか?</b></summary>

**回答**: Web サイトに QnA Maker サービスを Web チャット コントロールとして埋め込むには、次の手順に従ってください。

1. [こちら](./Quickstarts/create-publish-knowledge-base.md)の手順に従って、FAQ ボットを作成します。
2. [こちら](/azure/bot-service/bot-service-channel-connect-webchat)の手順に従って、Web チャットを有効にします。

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)


<details>
<summary><b>QnA Maker を利用するには Bot Framework を使う必要がありますか?</b></summary>

**回答**: いいえ。QnA Maker に関して [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) を使用する必要はありません。 ただし、QnA Maker は [Azure Bot Service](/azure/bot-service/) に用意されているいくつかのテンプレートの 1 つとして提供されています。 Bot Service では、Microsoft Bot Framework を使用してインテリジェントなボットを迅速に開発し、サーバーレス環境で実行することができます。

</details>

<details>
<summary><b>QnA Maker で新しいボットを作成するにはどうすればよいですか?</b></summary>

**回答**: Azure Bot Service でボットを作成するには、[こちら](./Quickstarts/create-publish-knowledge-base.md)のドキュメントの手順に従ってください。

</details>

<details>
<summary><b>既存の Azure Bot Service で別のナレッジ ベースを使用するにはどうすればよいですか?</b></summary>

**回答**: 次のナレッジ ベースに関する情報が必要です。

* ナレッジ ベース ID。
* ナレッジ ベースの公開されたエンドポイント カスタム サブドメイン名 (`host` と呼ばれます)。公開後に **[設定]** ページで見つかります。
* ナレッジ ベースの公開されたエンドポイント キー - 公開後に **[設定]** ページで見つかります。

この情報を使用して、Azure portal でご利用のボットのアプリ サービスにアクセスします。 **[設定] -> [構成] -> [アプリケーション設定]** で、これらの値を変更します。

ナレッジ ベースのエンドポイント キーは、ABS サービスで `QnAAuthkey` としてラベルが付けられます。

</details>

<details>
<summary><b>2 つ以上のクライアント アプリケーションでナレッジ ベースを共有できますか?</b></summary>

**回答**: はい。ナレッジ ベースには、任意の数のクライアントからクエリを実行できます。 ナレッジ ベースからの応答が低速であるか、タイムアウトになっている場合は、ナレッジ ベースに関連付けられているアプリ サービスのサービス レベルをアップグレードすることを検討してください。

</details>

<details>
<summary><b>QnA Maker サービスを Web サイトに埋め込むにはどうすればよいですか?</b></summary>

**回答**: Web サイトに QnA Maker サービスを Web チャット コントロールとして埋め込むには、次の手順に従ってください。

1. [こちら](./Quickstarts/create-publish-knowledge-base.md)の手順に従って、FAQ ボットを作成します。
2. [こちら](/azure/bot-service/bot-service-channel-connect-webchat)の手順に従って、Web チャットを有効にします。

---

## <a name="data-storage"></a>データ ストレージ

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (安定版リリース)](#tab/v1)

<details>
<summary><b>どのようなデータが、どこに保存されますか。</b></summary>

**回答**:

QnA Maker サービスを作成するときに、Azure リージョンを選択しました。 ナレッジ ベースとログ ファイルが、このリージョンに格納されます。

</details>

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker マネージド (プレビュー リリース)](#tab/v2)

<details>
<summary><b>どのようなデータが、どこに保存されますか。</b></summary>

**回答**:

QnA Maker サービスを作成するときに、Azure リージョンを選択しました。 ナレッジ ベースとログ ファイルが、このリージョンに格納されます。

</details>

---
