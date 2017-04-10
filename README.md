## <a name="microsoft-open-source-code-of-conduct"></a>Microsoft オープン ソースの倫理規定

このプロジェクトでは、[Microsoft オープン ソースの倫理規定](https://opensource.microsoft.com/codeofconduct/)を採用しています。
詳細については、[倫理規定についてよくある質問](https://opensource.microsoft.com/codeofconduct/faq/)を参照するか、[opencode@microsoft.com](mailto:opencode@microsoft.com) 宛てに質問またはコメントをお送りください。

# <a name="azure-technical-documentation-contributor-guide"></a>Azure 技術ドキュメント作成協力者ガイド
この GitHub リポジトリには、[https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) で公開されている Azure 技術ドキュメントのソースが格納されています。

また、技術ドキュメントの作成にご協力いただく際に役立つガイダンスも納められています。 共同作成者ガイドに掲載されている記事の一覧については、[インデックス](contributor-guide/contributor-guide-index.md)をご覧ください。

## <a name="contribute-to-azure-documentation"></a>Azure ドキュメントの作成への協力
Azure ドキュメントにご興味をお持ちくださり、ありがとうございます。

* [作成協力の方法](#ways-to-contribute)
* [倫理規定](#code-of-conduct)
* [Azure コンテンツの作成協力について](#about-your-contributions-to-azure-content)
* [リポジトリの構造](#repository-organization)
* [GitHub、Git、およびこのリポジトリの使い方](#use-github-git-and-this-repository)
* [マークダウンを使用してトピックの書式を設定する方法](#how-to-use-markdown-to-format-your-topic)
* [その他のリソース](#more-resources)
* [共同作成者ガイドに掲載されている全記事のインデックス](contributor-guide/contributor-guide-index.md) (新しいページが開きます)

## <a name="ways-to-contribute"></a>作成協力の方法
次のように、[Azure のドキュメント](https://docs.microsoft.com/azure)に対する更新を提案できます。

* GitHub ユーザー インターフェイスからも技術記事の作成に簡単にご協力いただけます。 目的の記事をこのリポジトリで見つけるか、[https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) で記事にアクセスして GitHub 内のソースにアクセスするためのリンクをクリックしてください。
* 既存の記事に大幅な変更を加える場合、画像を追加または変更する場合、新しい記事を投稿する場合は、このリポジトリのフォーク、Git Bash と Markdown Pad のインストール、いくつかの Git コマンドの習得が必要になります。

## <a name="about-your-contributions-to-azure-content"></a>Azure コンテンツの作成協力について
### <a name="minor-corrections"></a>軽微な修正
このリポジトリに含まれているドキュメントとコード例に関して提案された軽微な修正や明確化は、[docs.microsoft.com の使用条件](https://docs.microsoft.com/legal/termsofuse)の対象になります。

### <a name="larger-submissions"></a>より大規模な提案
ドキュメントとコード例に関する新しい記事または大幅な変更についてプル リクエストが送信された場合、Microsoft の従業員以外の共同作成者の方には、オンラインの Contribution License Agreement (CLA) の提出をお願いする GitHub のコメントが送信されます。 プル リクエストが正しく受理されるように、オンライン フォームへのご記入をお願いいたします。

## <a name="repository-organization"></a>リポジトリの構造
azure-docs リポジトリ内のコンテンツは、https://docs.microsoft.com/azure のドキュメント構造に準拠しています。 このリポジトリには、2 つのルート フォルダーがあります。

### <a name="articles"></a>\articles
*\articles* フォルダーには、拡張子が *.md* のマークダウン ファイルとして書式設定されたドキュメント記事が格納されています。 記事は、通常、Azure サービス別に分類されています。

記事は、厳密なファイルの名前付け規則に従う必要があります。詳細については、[ファイルの名前付け規則に関するガイダンス](contributor-guide/file-names-and-locations.md)をご覧ください。

*\articles* フォルダーには、ルート ディレクトリ記事のメディア ファイル用に *\media* フォルダーが含まれており、そのフォルダー内には各記事の画像が格納されたサブフォルダーが含まれています。  サービス別フォルダーにも、それぞれのサービス フォルダー内の記事用のメディア フォルダーが含まれています。 記事の画像フォルダーの名前は、記事ファイルの名前から *.md* ファイル拡張子を除いたものが設定されています。

### <a name="includes"></a>\includes
繰り返し使用できるコンテンツ セクションを作成して、1 つ以上の記事に含めることができます。 [技術コンテンツで使用されるカスタム拡張](contributor-guide/custom-markdown-extensions.md)に関するトピックをご覧ください。

### <a name="markdown-templates"></a>\markdown templates
このフォルダーには、記事に必要な基本のマークダウン書式が設定された標準のマークダウン テンプレートが格納されています。

### <a name="contributor-guide"></a>\contributor-guide
このフォルダーには、作成協力者ガイドを構成している記事が格納されています。

## <a name="use-github-git-and-this-repository"></a>GitHub、Git、およびこのリポジトリの使い方
共同作成する方法、GitHub の UI を使用して軽微な変更を投稿する方法、より大規模な投稿のためにリポジトリのフォークとクローンを行う方法については、[GitHub の記事作成用ツールのインストールとセットアップ](contributor-guide/tools-and-setup.md)に関するトピックをご覧ください。

GitBash をインストールしてローカルで作業する場合は、[記事の新規作成または既存の記事の更新のための Git コマンド](contributor-guide/git-commands-for-master.md)に関するトピックに記載されている、ローカルの作業ブランチを新たに作成し、変更を加え、その変更をメイン ブランチに送信する手順をご覧ください。

### <a name="branches"></a>ブランチ
ローカルの作業ブランチを作成する際は、変更のスコープを明確に設定することをお勧めします。 作業の流れを効率の良いものにし、マージが競合する可能性を減らすために、各ブランチではコンセプトや記事を 1 つに制限してください。  新しいブランチのスコープに適しているのは、次のような作業です。

* 記事 (および関連する画像) の新規作成
* 記事の誤字脱字や文法の編集
* 多数の記事に対する同一の書式変更の適用 (例: 新しい著作権用フッター)

## <a name="how-to-use-markdown-to-format-your-topic"></a>マークダウンを使用してトピックの書式を設定する方法
このリポジトリ内のすべての記事には、GitHub 用のマークダウンが使用されています。  以下のリソースが役立ちます。

* [マークダウンの基本](https://help.github.com/articles/markdown-basics/)
* [印刷可能なマークダウンのチートシート](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)

## <a name="article-metadata"></a>記事のメタデータ
記事のメタデータを使用すると、作成者の属性、共同作成者の属性、階層リンク、記事の説明、SEO 最適化などの特定の機能に加え、Microsoft がコンテンツのパフォーマンス評価に使用するレポート機能を利用できます。 このように、メタデータは重要性の高い要素です。 [こちらのリンクから、メタデータを正しく活用するためのガイダンスにアクセスできます](contributor-guide/article-metadata.md)。

### <a name="labels"></a>ラベル
自動化されたラベルはプル要求に割り当てられ、Microsoft がプル要求のワークフローを管理したり、プル要求で何が行われているかを把握できるようにしたりするのに役立ちます。

* Contribution License Agreement 関連
  * cla-not-required: 変更は比較的軽微なため、CLA への署名は不要です。
  * cla-required: 変更の範囲が比較的広いため、CLA への署名が必要です。
  * cla-signed: 共同作成者は CLA に署名済みのため、プル要求はレビューのために進めることができるようになりました。
* 重要なラベル: PnP、Modern Apps、TDC などのラベルは、プル要求を確認する必要がある内部組織によってプル要求を分類するのに役立ちます。
* Change sent to author: 作成者には、保留中のプル要求が通知されます。

## <a name="more-resources"></a>その他のリソース
すべてのガイダンス トピックについては、[こちらの共同作成者ガイドのインデックス](contributor-guide/contributor-guide-index.md)をご覧ください。

