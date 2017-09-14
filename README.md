## <a name="microsoft-open-source-code-of-conduct"></a>Microsoft オープン ソースの倫理規定

このプロジェクトでは、[Microsoft オープン ソースの倫理規定](https://opensource.microsoft.com/codeofconduct/)を採用しています。
詳細については、[倫理規定についてよくある質問](https://opensource.microsoft.com/codeofconduct/faq/)を参照するか、[opencode@microsoft.com](mailto:opencode@microsoft.com) 宛てに質問またはコメントをお送りください。

## <a name="contribute-to-azure-technical-documentation"></a>Azure 技術ドキュメントの作成への協力
コミュニティ (ユーザー、顧客、パートナー、コア Azure 製品単位以外の MSFT 従業員など)、およびコア Azure 製品単位の作業を行っている従業員からの投稿を歓迎します。 所属によって異なる投稿方法

* **コミュニティ - マイナー更新**: 親切心からマイナー更新を投稿する場合は、目的の記事をこのリポジトリで見つけるか、[https://docs.microsoft.com/azure](https://docs.microsoft.com/azure) で記事にアクセスして **[編集]** リンクをクリックし、その記事の GitHub ソースにアクセスします。 その後、GitHub UI を使用して、更新を加えます。 または、リポジトリをフォークして、フォークから更新プログラムを送信できます。

* **コミュニティ - 新しい記事**: Azure コミュニティに参加していて、新しい記事を作成する場合は、パブリックとプライベートのリポジトリの作業を組み合わせて、新しいコンテンツを提供できるように、従業員と協力する必要があります。

* **従業員**: Azure サービスの製品チームのテクニカル ライター、プログラム マネージャー、または開発者であり、技術記事の投稿または作成を担当している場合は、プライベート リポジトリ (https://github.com/MicrosoftDocs/azure-docs-pr) を使用する必要があります。 既存の記事に大幅な変更を加える場合、画像を追加または変更する場合、新しい記事を投稿する場合は、このリポジトリのフォーク、Git Bash と Markdown editor のインストール、いくつかの Git コマンドの習得が必要になります。 詳細については、[内部の共同制作者のガイド](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master)に関するページを参照してください。


## <a name="about-your-contributions-to-azure-content"></a>Azure コンテンツの作成協力について
### <a name="minor-corrections"></a>軽微な修正
このリポジトリに含まれているドキュメントとコード例に関して提案された軽微な修正や明確化は、[docs.microsoft.com の使用条件](https://docs.microsoft.com/legal/termsofuse)の対象になります。

### <a name="larger-submissions"></a>より大規模な提案
ドキュメントとコード例に関する新しい記事または大幅な変更についてプル リクエストが送信された場合、Microsoft の従業員以外の共同作成者の方には、オンラインの Contribution License Agreement (CLA) の提出をお願いする GitHub のコメントが送信されます。 プル リクエストが正しく受理されるように、オンライン フォームへのご記入をお願いいたします。

## <a name="tools-and-setup"></a>ツールとセットアップ
コミュニティの共同作成者は、GitHub UI を使用したり、投稿するリポジトリをフォークしたりすることができます。 従業員は、技術ドキュメント セットに投稿する方法について、[内部の共同作成者のガイド](https://review.docs.microsoft.com/en-us/help/contribute/?branch=master)に関するページを確認する必要があります。

## <a name="repository-organization"></a>リポジトリの構造
azure-docs リポジトリ内のコンテンツは、https://docs.microsoft.com/azure のドキュメント構造に準拠しています。 このリポジトリには、2 つのルート フォルダーがあります。

### <a name="articles"></a>\articles
*\articles* フォルダーには、拡張子が *.md* のマークダウン ファイルとして書式設定されたドキュメント記事が格納されています。 記事は、通常、Azure サービス別に分類されています。

*\articles* フォルダーには、ルート ディレクトリ記事のメディア ファイル用に *\media* フォルダーが含まれており、そのフォルダー内には各記事の画像が格納されたサブフォルダーが含まれています。  サービス別フォルダーにも、それぞれのサービス フォルダー内の記事用のメディア フォルダーが含まれています。 記事の画像フォルダーの名前は、記事ファイルの名前から *.md* ファイル拡張子を除いたものが設定されています。

### <a name="includes"></a>\includes
繰り返し使用できるコンテンツ セクションを作成して、1 つ以上の記事に含めることができます。 

## <a name="how-to-use-markdown-to-format-your-topic"></a>マークダウンを使用してトピックの書式を設定する方法
このリポジトリ内のすべての記事には、GitHub 用のマークダウンが使用されています。  以下のリソースが役立ちます。

* [マークダウンの基本](https://help.github.com/articles/markdown-basics/)
* [印刷可能なマークダウンのチートシート](./contributor-guide/media/documents/markdown-cheatsheet.pdf?raw=true)


## <a name="labels"></a>ラベル
パブリックの azure-docs リポジトリで、自動化されたラベルはプル要求に割り当てられ、Microsoft がプル要求のワークフローを管理したり、プル要求で何が行われているかを把握できるようにしたりするのに役立ちます。

* Contribution License Agreement 関連
  * cla-not-required: 変更は比較的軽微なため、CLA への署名は不要です。
  * cla-required: 変更の範囲が比較的広いため、CLA への署名が必要です。
  * cla-signed: 共同作成者は CLA に署名済みのため、プル要求はレビューのために進めることができるようになりました。
* Change sent to author: 作成者には、保留中のプル要求が通知されます。
* ready-to-merge: プル要求のレビュー チームによるレビューの準備ができています。


