---
title: ソース管理と開発のブランチ - LUIS
description: Language Understanding (LUIS) アプリをソース管理下で維持する方法。 開発ブランチで作業中の LUIS アプリに更新プログラムを適用する方法。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 68d88ef667da9f22d3e3a17f10036693fcca0c3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932524"
---
# <a name="devops-practices-for-luis"></a>LUIS の DevOps プラクティス

Language Understanding (LUIS) アプリを開発するソフトウェア エンジニアは、次のガイドラインに従って、[ソース管理](luis-concept-devops-sourcecontrol.md)、[自動ビルド](luis-concept-devops-automation.md)、[テスト](luis-concept-devops-testing.md)、[リリース管理](luis-concept-devops-automation.md#release-management)に DevOps プラクティスを適用することができます。

## <a name="source-control-and-branch-strategies-for-luis"></a>LUIS のソース管理およびブランチ戦略

DevOps が成功するための要因の 1 つは、[ソース管理](/azure/devops/user-guide/source-control)です。 ソース管理システムを使用すると、開発者はコードで共同作業したり、変更を追跡したりできます。 ブランチを使用すると、開発者は異なるバージョンのコード ベースを切り替えることができ、チームの他のメンバーとは独立して作業を行うことができます。 開発者が [pull request](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) を生成して 1 つのブランチから別のブランチへの更新を提案したとき、または変更がマージされたときに、それらを[自動ビルド](luis-concept-devops-automation.md)のトリガーにして、コードのビルドや継続的テストを実行できます。

このドキュメントで説明されている概念とガイダンスを使用することにより、ソース管理システムの変更を追跡しながら LUIS アプリを開発し、次のソフトウェア エンジニアリングのベスト プラクティスに従うことができます。

- **ソース管理**
  - LUIS アプリのソース コードは、人間が判読できる形式です。
  - 反復可能な方法でソースからモデルを構築できます。
  - ソース コード リポジトリでソース コードを管理できます。
  - オーサリング キーやサブスクリプション キーなどの資格情報とシークレットは、ソース コードに格納されません。

- **ブランチとマージ**
  - 開発者は、独立したブランチから作業できます。
  - 開発者は、複数のブランチで同時に作業できます。
  - リベースまたはマージにより、LUIS アプリに対する変更をあるブランチから別のブランチに統合できます。
  - 開発者は、PR を親ブランチにマージできます。

- **バージョン管理**
  - 大規模なアプリケーションの各コンポーネントは、個別にバージョン管理する必要があります。これにより、開発者は、バージョン番号を調べるだけで、破壊的変更や更新を検出できます。

- **コード レビュー**
  - PR の変更は、PR を受け入れる前にレビューできる、人間が判読できるソース コードとして提示されます。

## <a name="source-control"></a>ソース管理

LUIS アプリの[アプリ スキーマ定義](./app-schema-definition.md)をソース コード管理システムで維持するには、アプリの [LUDown 形式 (`.lu`)](/azure/bot-service/file-format/bot-builder-lu-file-format) 表現を使用します。 人間が判読できるので、`.lu` 形式は `.json` 形式より好まれます。これにより、PR で変更を行ってレビューするのが容易になります。

### <a name="save-a-luis-app-using-the-ludown-format"></a>LUDown 形式を使用して LUIS アプリを保存する

LUIS アプリを `.lu` 形式で保存し、ソース管理下に置くには:

- いずれか: [LUIS ポータル](https://www.luis.ai/)から `.lu` として[アプリのバージョンをエクスポート](./luis-how-to-manage-versions.md#other-actions)し、それをソース管理リポジトリに追加します

- またはテキスト エディターを使用して LUIS アプリ用の `.lu` ファイルを作成し、それをソース管理リポジトリに追加します

> [!TIP]
> LUIS アプリの JSON エクスポートを使用する場合は、[それを LUDown に変換する](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert)ことができます。  意図と発話がアルファベット順に並べ替えられるようにするには、`--sort` オプションを使用します。  
> LUIS ポータルに組み込まれている **.LU** エクスポート機能により、出力は既に並べ替えられていることに注意してください。

### <a name="build-the-luis-app-from-source"></a>LUIS アプリをソースからビルドする

LUIS アプリの場合、"*ソースからビルドする*" とは、[`.lu` ソースをインポートすることによって LUIS アプリの新しいバージョンを作成](./luis-how-to-manage-versions.md#import-version)し、[バージョンをトレーニング](./luis-how-to-train.md)して、[それを発行する](./luis-how-to-publish-app.md)ことを意味します。 これは、LUIS ポータルまたはコマンド ラインで行うことができます。

- LUIS ポータルを使用して、ソース管理からアプリの [`.lu` バージョンをインポート](./luis-how-to-manage-versions.md#import-version)し、[トレーニング](./luis-how-to-train.md)して、アプリを[発行](./luis-how-to-publish-app.md)します。

- コマンド ラインまたは CI/CD ワークフローで [LUIS 用の Bot Framework コマンド ライン インターフェイス](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS)を使用して、ソース管理から LUIS アプリケーションにアプリの `.lu` バージョンを[インポート](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport)し、[トレーニング](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun)して、アプリを[発行](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish)します。

### <a name="files-to-maintain-under-source-control"></a>ソース管理で保持するファイル

LUIS アプリケーションの次の種類のファイルを、ソース管理で保持する必要があります。

- LUIS アプリケーション用の `.lu` ファイル

- [単体テスト定義ファイル](luis-concept-devops-testing.md#writing-tests) (発話と予想される結果)

- パフォーマンス テストに使用される[バッチ テスト ファイル](./luis-how-to-batch-test.md#batch-test-file) (発話と予想される結果)

### <a name="credentials-and-keys-are-not-checked-in"></a>資格情報とキーはチェックインしない

承認されていないユーザーに見られる可能性があるリポジトリにチェックインするファイルには、サブスクリプション キーまたはそれに類する機密性の値を含めないでください。 チェックインしてはならないキーや他の値としては、次のものがあります。

- LUIS のオーサリング キーと予測キー
- LUIS のオーサリング エンドポイントと予測エンドポイント
- Azure サブスクリプション キー
- アクセス トークン (自動化認証に使用される Azure [サービス プリンシパル](/cli/azure/ad/sp)用のトークンなど)

#### <a name="strategies-for-securely-managing-secrets"></a>シークレットを安全に管理するための戦略

シークレットを安全に管理するための戦略は次のとおりです。

- Git バージョン コントロールを使用している場合は、ランタイム シークレットをローカル ファイルに格納し、ファイル名と一致するパターンを [.gitignore](https://git-scm.com/docs/gitignore) ファイルに追加することで、ファイルがチェックインされないようにできます
- オートメーション ワークフローでは、そのオートメーション テクノロジによって提供されるパラメーター構成にシークレットを安全に格納できます。 たとえば、[GitHub Actions](https://github.com/features/actions) を使用している場合は、[GitHub シークレット](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets)にシークレットを安全に格納できます。

## <a name="branching-and-merging"></a>ブランチとマージ

Git のような分散バージョン コントロール システムでは、チーム メンバーが、他のユーザーと共有する開発ブランチを通じて、コード変更の発行、共有、レビュー、反復処理を柔軟に行うことができます。 チームに適した [Git ブランチ戦略](/azure/devops/repos/git/git-branching-guidance)を採用します。

どのブランチ戦略を採用する場合でも、あらゆるものの中で重要な原則は、チーム メンバーは、他のブランチで行われている作業とは独立して、"*機能ブランチ*" 内のソリューションで作業を行うことができるということです。

LUIS プロジェクトでブランチでの独立した作業をサポートするには:

- **メイン ブランチに独自の LUIS アプリがある。** このアプリはプロジェクトのソリューションの現在の状態を表し、その現在のアクティブなバージョンは常に、メイン ブランチ内の `.lu` ソースにマップしている必要があります。 このアプリの `.lu` ソースに対するすべての更新をレビューしてテストし、いつでもこのアプリを運用環境などのビルド環境にデプロイできるようにする必要があります。 `.lu` の更新を機能ブランチからメインにマージするときは、LUIS アプリで新しいバージョンを作成し、[バージョン番号を上げる](#versioning)必要があります。

- **各機能ブランチでは、LUIS アプリの独自のインスタンスを使用する必要がある**。 開発者は、他のブランチで作業している開発者に影響を与える恐れなしに、機能ブランチでこのアプリの作業を行います。 この "開発ブランチ" アプリは、機能ブランチを削除するときに削除する必要がある作業コピーです。

![Git の機能ブランチ](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>開発者は独立したブランチから作業できる

開発者は、次のようにすることで、他のブランチから独立して LUIS アプリの更新を行うことができます。

1. メイン ブランチから機能ブランチを作成します (ブランチ戦略に依存し、通常はメインまたは開発)。

1. 機能ブランチでの作業をサポートするためだけに、[LUIS ポータルで新しい LUIS アプリを作成します](./luis-how-to-start-new-app.md) ("*開発ブランチ アプリ*")。

   * ソリューションの `.lu` ソースがブランチに既に存在する場合、それはプロジェクトで前に別のブランチで行われた作業の後で保存されたものなので、`.lu` ファイルをインポートして開発ブランチの LUIS アプリを作成します。

   * 新しいプロジェクトで作業を始める場合、メイン LUIS アプリの `.lu` ソースはリポジトリにまだありません。 機能ブランチでの作業が完了したらポータルから開発ブランチ アプリをエクスポートすることによって `.lu` ファイルを作成し、PR の一部としてそれを送信します。

1. 開発ブランチ アプリのアクティブなバージョンで作業を行い、必要な変更を実装します。 機能ブランチのすべての作業を、開発ブランチ アプリの 1 つのバージョンでのみ行うことをお勧めします。 開発ブランチ アプリで複数のバージョンを作成する場合は、PR を送るときに、チェックインする変更が含まれているバージョンを追跡するように注意してください。

1. 更新をテストします。開発ブランチ アプリのテストの詳細については、「[LUIS DevOps のテスト](luis-concept-devops-testing.md)」を参照してください。

1. 開発ブランチ アプリのアクティブ バージョンを、[バージョン リスト](./luis-how-to-manage-versions.md)から `.lu` としてエクスポートします。

1. 更新をチェックインし、更新のピア レビューを依頼します。 GitHub を使用している場合は、[pull request](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests) を送ります。

1. 変更が承認されたら、更新をメイン ブランチにマージします。 この時点で、メインの更新された `.lu` を使用して、"*メイン*" LUIS アプリの新しい [バージョン](./luis-how-to-manage-versions.md)を作成します。 バージョン名の設定に関する考慮事項については、「[バージョン管理](#versioning)」を参照してください。

1. 機能ブランチを削除するときは、機能ブランチでの作業用に作成した開発ブランチ LUIS アプリを削除することをお勧めします。

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>開発者は複数のブランチで同時に作業できる

前の「[開発者は独立したブランチから作業できる](#developers-can-work-from-independent-branches)」で説明したパターンに従う場合は、各機能ブランチで固有の LUIS アプリケーションを使用します。 現在作業しているブランチに対する正しい開発ブランチ LUIS アプリに切り替えるようにすれば、1 人の開発者が複数のブランチで同時に作業することができます。

機能ブランチと、機能ブランチでの作業用に作成する開発ブランチ LUIS アプリの両方で同じ名前を使用することをお勧めします。このようにすれば、誤って間違ったアプリで作業を行う可能性が低くなります。

前述のように、わかりやすくするため、各開発ブランチ アプリでは 1 つのバージョンで作業することをお勧めします。 複数のバージョンを使用する場合は、開発ブランチ アプリを切り替えるときに、正しいバージョンをアクティブ化するように注意してください。

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>複数の開発者が同じブランチで同時に作業できる

複数の開発者が同時に同じ機能ブランチで作業するのをサポートできます。

- 開発者は、同じ機能ブランチをチェックアウトし、通常どおり、作業が行われている間、自分自身または他の開発者が送信した変更をプッシュおよびプルします。

- 前の「[開発者は独立したブランチから作業できる](#developers-can-work-from-independent-branches)」で説明したパターンに従う場合、このブランチでは固有の LUIS アプリケーションを使用して開発をサポートします。 その "開発ブランチ" LUIS アプリは、開発チームで最初に機能ブランチでの作業を始めるメンバーによって作成されます。

- 開発ブランチ LUIS アプリに[チーム メンバーを共同作成者として追加します](./luis-how-to-collaborate.md)。

- 機能ブランチでの作業が終わったら、アクティブなバージョンの開発ブランチ LUIS アプリを[バージョン リスト](./luis-how-to-manage-versions.md)から `.lu` としてエクスポートした後、更新された `.lu` ファイルをリポジトリに保存し、変更をチェックインして PR します。

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>リベースまたはマージを使用して、あるブランチから別のブランチに変更を組み込む

自分用の機能ブランチを作成した後で、別のブランチで作業しているチームの他の開発者が、`.lu` ソースを更新し、それらをメイン ブランチにマージした可能性があります。 自分の機能ブランチ内で独自の変更を続ける前に、それらの変更を自分の作業バージョンに組み込むことができます。 他のコード資産と同じ方法で、[メインへのリベースまたはマージ](https://git-scm.com/book/en/v2/Git-Branching-Rebasing)により、それを行うことができます。 LUDown 形式の LUIS アプリは人間が判読できるため、標準のマージ ツールを使用したマージがサポートされています。

機能ブランチで LUIS アプリをリベースする場合は、次のヒントに従ってください。

- リベースまたはマージを行う前に、まずポータルからアプリを再エクスポートすることで、アプリの `.lu` ソースのローカル コピーに、LUIS ポータルを使用して適用した最新の変更がすべて含まれていることを確認します。 そうすれば、ポータルで行った変更で、まだエクスポートされていないものが失われないようにすることができます。

- マージの間に、標準ツールを使用してマージの競合を解決します。

- 自分だけの変更の適用を続けるときに、更新されたアプリで作業が行われるように、リベースまたはマージが完了した後で、アプリをポータルに再度インポートすることを忘れないでください。

### <a name="merge-prs"></a>PR をマージする

PR が承認されたら、変更を自分のメイン ブランチにマージできます。 LUIS アプリの LUDown ソースに適用される特別な考慮事項はありません。人間が判読できるので、標準のマージ ツールを使用するマージがサポートされます。 マージの競合は、他のソース ファイルと同じ方法で解決できます。

PR をマージした後は、次のようにクリーンアップすることをお勧めします。

- リポジトリ内のブランチを削除します

- 機能ブランチでの作業用に作成した "開発ブランチ" LUIS アプリを削除します。

アプリケーション コード資産と同じ方法で、LUIS アプリの更新に合わせて、単体テストを作成する必要があります。 継続的インテグレーション ワークフローを使用して、次のテストを行う必要があります。

- PR がマージされる前の PR の更新
- PR が承認され、変更がメインにマージされた後の、メイン ブランチ LUIS アプリ。

LUIS DevOps のテストの詳細については、[LUIS 用の DevOps のテスト](luis-concept-devops-testing.md)に関するページを参照してください。 ワークフローの実装の詳細については、[LUIS DevOps 用のオートメーション ワークフロー](luis-concept-devops-automation.md)に関するページを参照してください。

## <a name="code-reviews"></a>コード レビュー

LUDown 形式の LUIS アプリは人間が判読できるものであり、レビューに適した PR での変更の通信がサポートされています。 単体テスト ファイルも LUDown 形式で記述され、やはり PR で簡単にレビューできます。

## <a name="versioning"></a>バージョン管理

アプリケーションは複数のコンポーネントで構成され、[Azure Bot Service](/azure/bot-service/bot-service-overview-introduction) で実行されるボット、[QnA Maker](https://www.qnamaker.ai/)、[Azure Speech Service](../speech-service/overview.md) などが含まれる場合があります。 疎結合アプリケーションの目標を達成するには、アプリケーションの各コンポーネントが個別にバージョン管理されるように、[バージョン コントロール](/azure/devops/learn/git/what-is-version-control)を使用します。このようにすると、開発者はバージョン番号を見るだけで、破壊的変更や更新を検出できます。 独自のリポジトリで管理している場合は、他のコンポーネントから独立して LUIS アプリを簡単にバージョン管理できます。

メイン ブランチの LUIS アプリには、バージョン管理スキームを適用する必要があります。 LUIS アプリの `.lu` に対する更新をメインにマージするときは、メイン ブランチ用の LUIS アプリの新しいバージョンに、その更新されたソースをインポートします。

メイン LUIS アプリのバージョンには、数値のバージョン管理スキームを使用することをお勧めします。次はその例です。

`major.minor[.build[.revision]]`

更新ごとに、バージョン番号の最後の桁をインクリメントします。

LUIS アプリの機能に対する変更の範囲を示すためには、メジャーとマイナーのバージョンを使用できます。

* メジャー バージョン: 新しい[意図](./luis-concept-intent.md)や[エンティティ](./luis-concept-entity-types.md)のサポートなどの重要な変更
* マイナー バージョン: 重要な新しいトレーニングの後など、下位互換性のある軽微な変更
* ビルド: 機能の変更はなく、ビルドが異なるだけ。

メイン LUIS アプリの最新リビジョンのバージョン番号を決定したら、新しいアプリのバージョンをビルドしてテストし、品質保証や運用など、異なるビルド環境で使用できるエンドポイントに発行する必要があります。 継続的インテグレーション (CI) ワークフローで、これらすべての手順を自動化することを強くお勧めします。

参照:
- LUIS アプリをテストしてリリースするための CI ワークフローの実装方法の詳細については、[オートメーション ワークフロー](luis-concept-devops-automation.md)に関する記事。
- LUIS アプリをデプロイする方法については、「[リリース管理](luis-concept-devops-automation.md#release-management)。

### <a name="versioning-the-feature-branch-luis-app"></a>"機能ブランチ" LUIS アプリのバージョン管理

機能ブランチでの作業をサポートするために作成した "開発ブランチ" LUIS アプリを使用している場合、作業が完了したらアプリをエクスポートし、更新された `'lu` を PR に含めます。 PR をメインにマージした後、リポジトリ内のブランチと "開発ブランチ" LUIS アプリを削除する必要があります。 このアプリは機能ブランチでの作業をサポートするためだけに存在するため、このアプリ内で適用する必要がある特定のバージョン管理スキームはありません。

PR での変更をメインにマージした時点で、メインに対するすべての更新が個別にバージョン管理されるように、バージョン管理を適用する必要があります。

## <a name="next-steps"></a>次のステップ

* [LUIS DevOps のテスト](luis-concept-devops-testing.md)について学習します
* [GitHub で DevOps for LUIS を実装する](luis-how-to-devops-with-github.md)方法を学習します
