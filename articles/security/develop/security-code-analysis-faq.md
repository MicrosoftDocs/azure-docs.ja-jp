---
title: Microsoft Azure Security Code Analysis のドキュメントに関する FAQ
description: この記事には、Security Code Analysis 拡張機能に関する FAQ が記載されています
author: vharindra
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 8038b7bd60ac771c798a1a8645022b0bf9e142a9
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934849"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問
ご不明な点がある場合は、 詳細を以下の FAQ でご確認ください。

## <a name="general-faqs"></a>一般的な FAQ

### <a name="can-i-install-the-extension-on-my-tfs-not-azure-devops-server"></a>この拡張機能を (Azure DevOps ではない) TFS サーバーにインストールすることはできますか? 

いいえ。この拡張機能は、TFS へのダウンロードとインストールには対応していません。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Microsoft Security Code Analysis は、ビルドに対して実行しなければなりませんか? 

場合によります。 分析ツールのタイプに応じて、ソース コードだけあれば十分なものと、ビルドの出力が必要になるものがあります。 たとえば、Credential Scanner ではコード リポジトリのフォルダー構造内のファイルが分析されるので、Credential Scanner ビルド タスクと Publish Security Analysis Logs ビルド タスクをスタンドアロンのビルドで実行して結果を得ることができます。
BinSkim など、ビルド後の成果物を分析するその他のツールでは、まずビルドが必要になります。

### <a name="can-i-break-my-build-when-results-are-found"></a>結果が判明した時点でビルドを中断することはできますか? 
はい。いずれかのツールでログ ファイルに問題 (所見) がレポートされた時点で、ビルドを中断できます。 Post-Analysis ビルド タスクを追加し、ビルドを中断させる対象ツールのチェック ボックスをオンにしてください。 Post-Analysis タスクの UI では、いずれかのツールからエラーがレポートされた時点か、警告やエラーがレポートされた時点でビルドが中断されるようにすることが可能です。

### <a name="how-are-the-command-line-arguments-different-in-azure-devops-than-they-are-in-the-standalone-desktop-tools"></a>Azure DevOps とスタンドアロンのデスクトップ ツールのコマンド ライン引数には、どのような差異がありますか? 

多くの場合、Azure DevOps のビルド タスクは、セキュリティ ツールのコマンド ライン引数を直接ラップしたものです。 デスクトップのコマンド ラインで正常にツールに渡されている引数であれば、ビルド タスクの引数入力に渡すことができます。
主な相違点の一覧を次に示します。
 - ツールは、エージェントのソース フォルダーである $(Build.SourcesDirectory) または %BUILD_SOURCESDIRECTORY% から実行されます。 例:C:\agent\_work\1\s 
 - 引数におけるパスは、上記ソース ディレクトリのルートに対する相対パスと、絶対パスのどちらでも指定できます。これには、ローカル リソースのデプロイ先がわかっているオンプレミス エージェントを実行するか、Azure DevOps のビルド変数を使用します
 - 出力ファイル パスやフォルダーはツールにより自動的に指定されます。出力パスを指定した場合、そのパスは削除され、ビルド エージェント上にある既知のログ保存場所へのパスに置き換えられます
 - ツールによっては、GUI の起動を防ぐことを目的としてオプションを追加または削除するなど、コマンド ライン パラメーターのサニタイジングや削除が行われています。

### <a name="can-i-run-a-build-task-for-example-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Azure DevOps ビルドの複数のリポジトリにわたってビルド タスク (Credential Scanner など) を実行することはできますか? 

いいえ。現時点では、単一のパイプライン内にある複数のリポジトリに対して、これらの安全な開発ツールを実行することはできません。

###  <a name="the-output-file-i-specified-is-not-being-created--i-cant-find-the-output-file-i-specified"></a>指定した出力ファイルが作成されない、または指定した出力ファイルが見つからない 

現在、ビルド タスクではユーザー入力はサニタイズされ、出力ファイルの生成先はビルド エージェント上の共通の場所に更新されます。 この場所の詳細については、次の質問を参照してください。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>ツールで生成された出力ファイルはどこに保存されますか? 

ビルド タスクでは、ビルド エージェント上にある既知の場所 ($(Agent.BuildDirectory)\_sdt\logs) への出力パスが自動的に追加されます。 この場所を標準として採用することで、コード分析ログを生成または使用する別のチームも確実にアクセスできるようにしています。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>ビルドをキューに追加して、ホステッド ビルド エージェント上でこれらのタスクを実行することはできますか? 

はい。この拡張機能に含まれるタスクとツールはすべて、ホステッド ビルド エージェント上で実行できます。

>[!NOTE]
> Anti-Malware ビルド タスクには、Windows Defender が有効になったビルド エージェントが必要です。Windows Defender は、"Hosted VS2017" 以降のビルド エージェントで有効となります (従来または VS2015 の "Hosted" エージェントでは動作しません)。これらのエージェントではシグネチャを更新することはできませんが、シグネチャは常に、比較的新しいもの (3 時間以上経過していないもの) である必要があります。
>

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>これらのビルド タスクを、(ビルド パイプラインではなく) リリース パイプラインの一部として実行することはできますか? 
ほとんどの場合、可能です。 ただし、成果物を発行するタスクについては、Azure DevOps でリリース パイプライン内から実行することはできません。"リリースで使用することができないカテゴリのタスクは、成果物を発行するタスクのみです。 これは、現時点で、リリース内での成果物の発行がサポートされてないためです"。
このため、"Publish Security Analysis Logs" タスクをリリース パイプラインから正常に実行することはできず、失敗し、理由を説明するエラー メッセージが表示されます。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>ビルド タスクではツールはどこからダウンロードされますか? 
ビルド タスクでは、a) 次の [Azure DevOps Package Management フィード](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)から、または Node Package Manager を使用してツールの NuGet パッケージをダウンロードします。Node Package Manager については、あらかじめビルド エージェントにインストールしておく必要があります (例: "npm install tslint")。

### <a name="what-effect-will-installing-the-extension-have-on-my-azure-devops-organization"></a>この拡張機能をインストールすると、自分の Azure DevOps 組織にはどのような影響がありますか? 

インストールした時点から、組織内のすべてのユーザーが、この拡張機能によって提供されるセキュリティ ビルド タスクを使用できるようになります。 Azure パイプラインを作成または編集するときに、これらのタスクをビルド タスクのコレクション一覧から追加できるようになります。 それ以外の点では、Azure DevOps 組織にこの拡張機能をインストールしても影響はありません。 アカウントやプロジェクトの設定、パイプラインが変更されることはありません。

### <a name="will-installing-the-extension-modify-my-existing-azure-pipelines"></a>この拡張機能をインストールすると、既存の Azure Pipelines が変更されますか? 

いいえ。 この拡張機能をインストールすると、Azure Pipelines にセキュリティ ビルド タスクを追加できるようになります。 ビルド プロセスにこれらのツールを統合するには、引き続きユーザーがビルド定義を追加または更新する必要があります。

## <a name="task-specific-faqs"></a>タスク固有の FAQ

このセクションでは、各ビルド タスクに固有の FAQ を示します。

### <a name="credential-scanner-faqs"></a>Credential Scanner に関する FAQ

#### <a name="what-are-common-suppressions-scenarios-and-examples"></a>一般的な抑制のシナリオと、その例を教えてください。 
最も一般的な抑制のシナリオのうちの 2 つを以下に詳しく示します。
##### <a name="suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>指定パス内での特定のシークレットの出現をすべて抑制する 
次のサンプルに示すように、Credential Scanner 出力ファイルに含まれるシークレットのハッシュ キーが必要です
   
        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "hash": "CLgYxl2FcQE8XZgha9/UbKLTkJkUh3Vakkxh2CAdhtY=",
                "_justification": "Secret used by MSDN sample, it is fake."
            }
          ]
        }

>[!WARNING]
> ハッシュ キーは、照合対象の値またはファイル コンテンツの一部から生成されます。 ソース コードを編集するとハッシュ キーが変更され、抑制ルールが無効になる可能性があります。 

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>指定ファイル内ですべてのシークレットを抑制するには (またはシークレット ファイル自体を抑制するには) 
ファイルは、ファイル名か、フル ファイル パス/ファイル名の後置部分で指定できます。 ワイルドカードはサポートされていません。 

**例** 

抑制対象のファイル: [InputPath]\src\JS\lib\angular.js 

有効な抑制ルール: 
- [InputPath]\src\JS\lib\angular.js -- 指定パス内にあるファイルを抑制する
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js -- 同名の全ファイルを抑制する

        {
            "tool": "Credential Scanner",
            "suppressions": [
            {
                "file": "\\files\\AdditonalSearcher.xml", 
                "_justification": "Additional CredScan searcher specific to my team"
            },
            {
                "file": "\\files\\unittest.pfx", 
                "_justification": "Legitimate UT certificate file with private key"
            }
          ]
        }      

>[!WARNING] 
> 今後、対象のファイルに追加されたシークレットもすべて自動的に抑制されます。 

#### <a name="what-are-recommended-secrets-management-guidelines"></a>推奨されるシークレット管理のガイドラインはどのようなものですか? 
ハード コーディングされたシークレットを早期に発見してリスクを軽減することは有益ですが、シークレットのチェックインを完全に防止できればさらに良いでしょう。 こうした観点から、Microsoft では、Visual Studio 向けの [Microsoft DevLabs 拡張機能](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio)の一部として、CredScan コード アナライザーをリリースしています。 早期プレビュー段階ではありますが、開発者はこのツールを使用すると、コード内のシークレット候補をインラインで検出し、これらの問題をリアルタイムで修復する機会を得られます。 詳細については、クラウドでのシークレットの安全な管理方法に関する[こちら](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)のブログを参照してください。 その他、シークレットの管理や、アプリケーション内から安全な方法で機密情報にアクセスするのに役立つリソースをいくつか以下に示します。 
 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD マネージド サービス ID](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure リソースの管理対象サービス ID (MSI)](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure マネージド サービス ID](../../app-service/overview-managed-identity.md)
 - [AppAuthentication ライブラリ](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>カスタムの検索ツールを作成することはできますか?

Credential Scanner では、通常は **buildsearchers.xml** ファイルで定義されているコンテンツ検索ツール一式が使用されます。 このファイルには、ContentSearcher オブジェクトを表すシリアル化された XML オブジェクトの配列が記載されています。 このプログラムには、十分にテストされた検索ツール一式が同梱されていますが、独自のカスタム検索ツールを作成することも可能です。 

コンテンツ検索ツールは以下のように定義されます。 

- **Name** – Credential Scanner の出力ファイルで使用される、検索ツールのわかりやすい名前。 検索ツールの名前にはキャメル ケースの名前付け規則を使うことをお勧めします。 
- **RuleId** – 不変的で不明瞭な検索ツールの ID。 
    - Credential Scanner の既定の検索ツールには、CSCAN0010、CSCAN0020、CSCAN0030 といったぐあいに RuleId が割り当てられています。最後の桁は、検索ツールについて正規表現でグループ化またはグループ分けする場合に備えて予約されています。
    - 独自の検索ツールの RuleId には次の形式で独自の名前空間を設定する必要があります。CSCAN-{Namespace}0010、CSCAN-{Namespace}0020、CSCAN-{Namespace}0030 などです。
    - 検索ツールの完全修飾名は、RuleId と検索ツール名を組み合わせたものになります。 例: CSCAN0010.KeyStoreFiles、CSCAN0020.Base64EncodedCertificate など
- **ResourceMatchPattern** – 検索ツールと照合するファイル拡張子の正規表現
- **ContentSearchPatterns** – 照合する正規表現文を含む文字列の配列。 検索パターンを定義しない場合、リソースの照合パターンに一致するすべてのファイルが返されます。
- **ContentSearchFilters** – 検索ツールごとの誤検知を除外するための正規表現文を含む文字列の配列。
- **Matchdetails** – 検索ツールの一致ごとに追記する説明メッセージや緩和手順。
- **Recommendation** – 一致に対する提案フィールドの内容を PREfast のレポート形式で指定します。
- **Severity** – 問題の重大度を表す整数 (1 が最高)。
![Credential Scanner の設定](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers-faqs"></a>Roslyn アナライザーに関する FAQ

#### <a name="what-are-the-most-common-errors-when-using-the-roslyn-analyzers-task"></a>Roslyn アナライザー タスクの使用時に発生する、最も一般的なエラーはどのようなものですか?

**エラー:プロジェクトは Microsoft.NETCore.App バージョン x.x.x を使用して復元されましたが、現在の設定では、バージョン y.y.y が代わりに使用されます。この問題を解決するには、復元およびこれ以降の操作 (ビルドや発行など) で同じ設定を使用していることをご確認ください。通常この問題は、ビルドや発行の実行時に RuntimeIdentifier プロパティを設定したが、復元時には設定していない場合に発生することがあります:**

Roslyn アナライザーはコンパイルの一環として実行されるので、ビルド マシンのソース ツリーがビルド可能な状態である必要があります。 メイン ビルドと Roslyn アナライザーの間にあるステップ ("dotnet.exe publish" など) で、ソース ツリーがビルド不能な状態になっている可能性があります。 たとえば、Nuget の復元を実行するステップを Roslyn アナライザーのステップの直前に複製すれば、ソース ツリーがビルド可能な状態に戻ります。

**"csc.exe" はエラー コード 1 で終了しました -- アナライザー AAAA のインスタンスは C:\BBBB.dll から作成できません:ファイルまたはアセンブリ 'Microsoft.CodeAnalysis, Version=X.X.X.X, Culture=neutral, PublicKeyToken=31bf3856ad364e35'、あるいはその依存関係の 1 つを読み込めませんでした。指定されたファイルが見つかりません。**

コンパイラで Roslyn アナライザーがサポートされていることを確認してください。 "csc.exe /version" で v2.6.x 以上が返される必要があります。 個々の .csproj ファイルで Microsoft.Net.Compilers からのパッケージを参照することにより、ビルド マシンの Visual Studio のインストールがオーバーライドされることがあります。 特定のバージョンのコンパイラを使用する意図がない場合は、Microsoft.Net.Compilers の参照を削除してください。 それ以外の場合は、参照対象のパッケージが v2.6.x 以上であることを確認してください。 エラー ログを確認してみてください。これは、csc.exe コマンド ラインの /errorlog: パラメーター (Roslyn ビルド タスクのログに記載) で指定されています。 例: /errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif

**The C# compiler is not recent enough (it must be >= 2.6) (C# コンパイラのバージョンが古くなっています (2.6 以上を使用する必要があります))**

最新バージョンの C# コンパイラは、 https://www.nuget.org/packages/Microsoft.Net.Compilers で公開されています。 使用中のインストール済みバージョンを確認するには、コマンド プロンプトで `C:\>csc.exe /version` を実行します。 バージョン 2.6 未満の Microsoft.Net.Compilers NuGet パッケージを参照していないか確認してください。

**MSBuild/VSBuild Logs Not Found (MSBuild/VSBuild のログが見つかりません)**

このタスクでは、そのしくみ上、MSBuild ビルド タスクで作成された MSBuild ログを Azure DevOps に対して照会する必要があります。 MSBuild ビルド タスクの直後にこのタスクを実行した場合、ログはまだ利用可能な状態になっていません。MSBuild ビルド タスクと Roslyn アナライザービルド タスクの間に、SecDevTools ビルド タスク (例: Binskim、マルウェア対策スキャン) などの他のビルド タスクを配置してください。 

## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、Microsoft Security Code Analysis サポートまでお問い合わせください (平日の午前 9:00 から午後 5:00 (太平洋標準時) まで営業)

  - オンボード - 使用開始手順については、担当のテクニカル アカウント マネージャーにお問い合わせください。 
  >[!NOTE] 
  >Microsoft の有料サポートにまだお申し込みいただいていない場合、または Phoenix カタログからサービスを購入できないサポート プランにお申し込みいただいている場合は、[サポート サービスのホーム ページ](https://www.microsoft.com/enterprise/services/support)で詳細をご覧ください。

  - サポート - [Microsoft Security Code Analysis サポート](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)までメールをお送りください