---
title: Microsoft Security Code Analysis ドキュメントの FAQ
description: この記事には、Microsoft Security Code Analysis 拡張機能に関する FAQ が記載されています。
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
ms.openlocfilehash: 846f0ecdd49fc1c501893209b60fa9acc8a32ed2
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242330"
---
# <a name="frequently-asked-questions"></a>よく寄せられる質問
ご不明な点がある場合は、 詳細については、次の FAQ を参照してください。

## <a name="general-faq"></a>一般的な FAQ

### <a name="can-i-install-the-extension-on-my-visual-studio-team-foundation-server-instance-instead-of-on-an-azure-devops-instance"></a>Azure DevOps インスタンスではなく、Visual Studio Team Foundation Server インスタンスに拡張機能をインストールすることはできますか?

いいえ。 この拡張機能は、Visual Studio Team Foundation Server 用のダウンロードとインストールには使用できません。

### <a name="do-i-have-to-run-microsoft-security-code-analysis-with-my-build"></a>Microsoft Security Code Analysis は、ビルドに対して実行しなければなりませんか? 

その可能性はあります。 分析ツールの種類によって異なります。 ソース コードのみが必要な場合や、ビルド出力が必要な場合があります。

たとえば、Credential Scanner (CredScan) では、コード リポジトリのフォルダー構造内のファイルが分析されます。 この分析があるので、CredScan を実行し、セキュリティ分析ログの発行ビルド タスクをスタンドアロン ビルドで実行して、結果を取得することができます。

ビルド後の成果物を分析する BinSkim などの他のツールでは、最初にビルドが必要です。

### <a name="can-i-break-my-build-when-results-are-found"></a>結果が判明した時点でビルドを中断することはできますか?

はい。 いずれかのツールでログ ファイルに問題がレポートされた時点で、ビルドの中断を挿入することができます。 分析後ビルド タスクを追加し、ビルドを中断するツールのチェック ボックスをオンにします。

分析後タスクの UI では、いずれかのツールからエラーが報告されたときのみ、またはエラーと警告の両方が報告されたときにビルドを中断することを選択できます。

### <a name="how-do-the-command-line-arguments-in-azure-devops-differ-from-those-arguments-in-the-standalone-desktop-tools"></a>Azure DevOps のコマンド ライン引数は、スタンドアロン デスクトップ ツールの引数とどのような違いがありますか? 

多くの場合、Azure DevOps のビルド タスクは、セキュリティ ツールのコマンド ライン引数を直接ラップしたものです。 通常、コマンド ライン ツールに渡すすべてのものを引数としてビルド タスクに渡すことができます。

主な相違点:

- ツールは、エージェント $(Build.SourcesDirectory) のソース フォルダーまたは %BUILD_SOURCESDIRECTORY% から実行されます。 たとえば C:\agent\_work\1\s です。
- 引数のパスは、前に示したソース ディレクトリのルートに対して相対的に指定できます。 絶対パスを指定することもできます。 絶対パスを取得するには、Azure DevOps のビルド変数を使用するか、ローカル リソースの既知の展開場所を指定してオンプレミスのエージェントを実行します。
- ツールでは、出力ファイルのパスまたはフォルダーが自動的に提供されます。 ビルド タスクの出力場所を指定したとしても、その場所はビルド エージェントのログの所定の場所へのパスに置き換えられます。
- 一部のツールでは、いくつかの追加のコマンド ライン引数が変更されています。 1 つの例として、GUI を起動しないようにするオプションが追加または削除される場合があります。

### <a name="can-i-run-a-build-task-like-credential-scanner-across-multiple-repositories-in-an-azure-devops-build"></a>Azure DevOps ビルドの複数のリポジトリにわたってビルド タスク (Credential Scanner など) を実行することはできますか?

いいえ。 1 つのパイプラインで複数のリポジトリにわたってセキュリティで保護された開発ツールを実行することはできません。

### <a name="the-output-file-i-specified-isnt-being-created-or-i-cant-find-the-output-file-i-specified"></a>指定した出力ファイルが作成されない、または指定した出力ファイルが見つからない

ビルド タスクでは、一部のユーザー入力がフィルター処理されます。 具体的には、生成された出力ファイルの場所が、ビルド エージェント上の共通の場所になるように更新されます。 この場所の詳細については、次の質問を参照してください。

### <a name="where-are-the-output-files-generated-by-the-tools-saved"></a>ツールで生成された出力ファイルはどこに保存されますか? 

ビルド タスクでは、ビルド エージェント上にある所定の場所 ($(Agent.BuildDirectory)\_sdt\logs) への出力パスが自動的に追加されます。 この場所で標準化されているため、コード分析ログを生成または使用するすべてのチームが出力にアクセスできます。

### <a name="can-i-queue-a-build-to-run-these-tasks-on-a-hosted-build-agent"></a>ビルドをキューに追加して、ホステッド ビルド エージェント上でこれらのタスクを実行することはできますか? 

はい。 この拡張機能に含まれるタスクとツールはすべて、ホステッド ビルド エージェント上で実行できます。

>[!NOTE]
> Anti-Malware Scanner ビルド タスクには、Windows Defender が有効なビルド エージェントが必要です。 Hosted Visual Studio 2017 以降では、このようなエージェントが用意されています。 ビルド タスクは、Visual Studio 2015 ホステッド エージェントでは実行されません。
>
> これらのエージェント上で署名を更新することはできませんが、署名の期間は常に 3 時間未満です。

### <a name="can-i-run-these-build-tasks-as-part-of-a-release-pipeline-as-opposed-to-a-build-pipeline"></a>これらのビルド タスクを、ビルド パイプラインではなくリリース パイプラインの一部として実行することはできますか?

ほとんどの場合、可能です。

ただし、リリース パイプライン内のタスクによって成果物が発行されるときは、Azure DevOps ではそれらのタスクのリリース パイプラインでの実行をサポートしません。 この不十分なサポートにより、セキュリティ分析ログの発行タスクがリリース パイプラインで正常に実行されません。 代わりに、エラー メッセージが表示され、タスクは失敗します。

### <a name="from-where-do-the-build-tasks-download-the-tools"></a>ビルド タスクではツールはどこからダウンロードされますか?

ビルド タスクでは、[Azure DevOps Package Management フィード](https://securitytools.pkgs.visualstudio.com/_packaging/SecureDevelopmentTools/nuget/v3/index.json)からツールの NuGet パッケージをダウンロードできます。 ビルド タスクでは、ノード パッケージ マネージャーを使用することもできます。これはビルド エージェントに事前にインストールしておく必要があります。 このようなインストールの例として、コマンド **npm install tslint** があります。

### <a name="what-effect-does-installing-the-extension-have-on-my-azure-devops-organization"></a>この拡張機能をインストールすると、自分の Azure DevOps 組織にはどのような影響がありますか? 

インストールすると、拡張機能によって提供されるセキュリティ ビルド タスクを組織内のすべてのユーザーが使用できるようになります。 Azure パイプラインを作成または編集する場合、これらのタスクはビルド - タスク コレクションの一覧から利用できます。 それ以外の点では、Azure DevOps 組織にこの拡張機能をインストールしても影響はありません。 インストールによって、アカウント設定、プロジェクト設定、またはパイプラインは変更されません。

### <a name="does-installing-the-extension-modify-my-existing-azure-pipelines"></a>この拡張機能をインストールすると、既存の Azure パイプラインは変更されますか? 

いいえ。 拡張機能をインストールすると、セキュリティ ビルド タスクをパイプラインに追加できるようになります。 ただし、ツールによってビルド プロセスを操作できるように、ビルド定義を追加または更新する必要があります。

## <a name="task-specific-faq"></a>タスク固有の FAQ

このセクションでは、ビルド タスクに固有の質問について説明します。

### <a name="credential-scanner"></a>Credential Scanner

#### <a name="what-are-common-suppression-scenarios-and-examples"></a>一般的な抑制のシナリオと、その例を教えてください。

ここでは、最も一般的な 2 つの抑制シナリオの詳細について説明します。

##### <a name="to-suppress-all-occurrences-of-a-given-secret-within-the-specified-path"></a>指定パス内での特定のシークレットの出現をすべて抑制するには

次の例に示すように、CredScan 出力ファイルのシークレットのハッシュ キーが必要です。

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

##### <a name="to-suppress-all-secrets-in-a-specified-file-or-to-suppress-the-secrets-file-itself"></a>指定ファイル内ですべてのシークレットを抑制するには、またはシークレット ファイル自体を抑制するには

ファイルの式には、ファイル名を指定できます。 また、完全なファイル パスまたはファイル名のベース名部分も指定できます。 ワイルドカードはサポートされていません。

次の例は、ファイル \<InputPath>\src\JS\lib\angular.js を抑制する方法を示しています

有効な抑制ルールの例を次に示します。

- \<InputPath>\src\JS\lib\angular.js - 指定パス内にあるファイルを抑制する
- \src\JS\lib\angular.js
- \JS\lib\angular.js
- \lib\angular.js
- angular.js - 同名の全ファイルを抑制する

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
> 今後、ファイルに追加されたシークレットもすべて自動的に抑制されます。

#### <a name="what-are-recommended-guidelines-for-managing-secrets"></a>シークレットの管理についてどのようなガイドラインが推奨されますか?

ハードコーディングされたシークレットを迅速に検出し、リスクを軽減することは役立ちます。 ただし、シークレットがまったく登録されないようにする方が推奨されます。

この点に役立つように、Microsoft では、Visual Studio 用の [Microsoft DevLabs 拡張機能](https://marketplace.visualstudio.com/items?itemName=VSIDEDevOpsMSFT.ContinuousDeliveryToolsforVisualStudio)の一部として、Credential Scanner Code Analyzer の早期プレビューをリリースしました。 このアナライザーは早期プレビュー リリースです。 これで、開発者は、コード内に存在する可能性があるシークレットをインラインで検出できるようになります。 この機能により、開発者はアナライザーを使用して、このような問題をリアルタイムで修正できるようになります。

詳細については、ブログ記事「[クラウドで安全にシークレットを管理する](https://devblogs.microsoft.com/visualstudio/managing-secrets-securely-in-the-cloud/)」を参照してください。

次のリソースは、機密情報を安全に管理し、アプリケーション内からシークレットにアクセスするために役立ちます。

 - [Azure Key Vault](../../key-vault/index.yml)
 - [Azure Active Directory (Azure AD)](../../sql-database/sql-database-aad-authentication.md)
 - [Azure AD マネージド サービス ID (MSI)](https://azure.microsoft.com/blog/keep-credentials-out-of-code-introducing-azure-ad-managed-service-identity/)
 - [Azure リソースのマネージド ID](../../active-directory/managed-identities-azure-resources/overview.md)
 - [Azure App Service および Azure Functions でのマネージド ID](../../app-service/overview-managed-identity.md)
 - [AppAuthentication ライブラリ](../../key-vault/service-to-service-authentication.md)

#### <a name="can-i-write-my-own-custom-searchers"></a>カスタムの検索ツールを作成することはできますか?

Credential Scanner では、通常は buildsearchers.xml ファイルで定義されているコンテンツ検索ツール一式が使用されます。 このファイルには、**ContentSearcher** オブジェクトを表すシリアル化された XML オブジェクトの配列が記載されています。 このプログラムは、適切に実証済みの一連の検索ツールと共に配布されます。 ただし、独自のカスタムの検索ツールを実装することもできます。

コンテンツ検索ツールは以下のように定義されます。

- **Name**:Credential Scanner の出力ファイルで使用される、検索ツールのわかりやすい名前。 検索ツール名には、キャメルケース形式の名前付け規則を使用することをお勧めします。
- **RuleId**:不変的で不明瞭な検索ツールの ID。
    - Credential Scanner の既定の検索ツールには、CSCAN0010、CSCAN0020、CSCAN0030 などの **RuleId** 値が割り当てられます。 最後の桁は、正規表現 (regex) を使用して検索ツール グループを結合または分割するために予約されています。
    - カスタマイズされた検索ツールの **RuleId** 値には、独自の名前空間が必要です。 たとえば、CSCAN-\<Namespace\>0010、CSCAN-\<Namespace\>0020、CSCAN-\<Namespace\>0030 などです。
    - 検索ツールの完全修飾名は、**RuleId** 値と検索ツール名を組み合わせたものです。 たとえば、CSCAN0010.KeyStoreFiles、CSCAN0020.Base64EncodedCertificate などです。
- **ResourceMatchPattern**:検索ツールに対してチェックするファイル拡張子の正規表現。
- **ContentSearchPatterns**:照合する regex ステートメントを含む文字列の配列。 検索パターンが定義されていない場合は、**ResourceMatchPattern** 値と一致するすべてのファイルが返されます。
- **ContentSearchFilters**:検索ツールごとの誤検知を除外するための正規表現文を含む文字列の配列。
- **MatchDetails**:検索ツールの各一致に追加される説明メッセージ、軽減手順、またはその両方。
- **推奨事項**:PREfast レポート形式を使用した一致の提案フィールドの内容。
- **[重大度]** :問題の重大度レベルを反映した整数。 最も高い重大度レベルの値は 1 です。

  ![Credential Scanner のセットアップを示す XML](./media/security-tools/6-credscan-customsearchers.png)

### <a name="roslyn-analyzers"></a>Roslyn アナライザー

#### <a name="what-are-common-errors-when-using-the-roslyn-analyzers-task"></a>Roslyn アナライザー タスクの使用時に発生する一般的なエラーはどのようなものですか?

##### <a name="the-project-was-restored-using-a-wrong-microsoftnetcoreapp-version"></a>不適切な Microsoft.NETCore.App バージョンを使用してプロジェクトが復元された

完全なエラー メッセージ:

"エラー: プロジェクトは Microsoft.NETCore.App バージョン *x.x.x* を使用して復元されましたが、現在の設定では、バージョン *y.y.y* が代わりに使用されます。 この問題を解決するには、復元およびこれ以降の操作 (ビルドや発行など) で同じ設定を使用していることをご確認ください。 通常、この問題は、ビルドや発行の実行時に RuntimeIdentifier プロパティを設定したが、復元時には設定していない場合に発生することがあります。

Roslyn アナライザー タスクはコンパイルの一環として実行されるので、ビルド マシンのソース ツリーがビルド可能な状態である必要があります。

メイン ビルドと Roslyn Analyzer の手順の間にある手順により、ソース ツリーがビルドできない状態になった可能性があります。 この余分な手順はおそらく **dotnet.exe publish** です。 Roslyn アナライザーの手順の直前に、NuGet の復元を実行する手順をコピーしてみてください。 この重複する手順によって、ソース ツリーがビルド可能な状態に戻る可能性があります。

##### <a name="cscexe-cant-create-an-analyzer-instance"></a>csc.exe でアナライザー インスタンスが作成されません。

完全なエラー メッセージ:

"'csc.exe' はエラー コード 1 で終了しました -- アナライザー *AAAA* のインスタンスは C:\\*BBBB*.dll から作成できません:ファイルまたはアセンブリ 'Microsoft.CodeAnalysis, Version=*X.X.X.X*, Culture=neutral, PublicKeyToken=31bf3856ad364e35'、あるいはその依存関係の 1 つを読み込めませんでした。 The system cannot find the file specified." ('cwd' の値 '/src' が無効です。指定されたファイルがシステムに見つかりません。)

コンパイラで Roslyn アナライザーがサポートされていることを確認してください。 コマンド **csc.exe/version** を実行したとき、2.6 以降のバージョン値が報告される必要があります。

場合によっては、.csproj ファイルでは、Microsoft.Net.Compilers からパッケージを参照することで、ビルド マシンの Visual Studio のインストールをオーバーライドすることができます。 特定のバージョンのコンパイラを使用しない場合は、Microsoft.Net.Compilers への参照を削除します。 それ以外の場合は、参照されているパッケージのバージョンも 2.6 以降であることを確認します。

**csc.exe /errorlog** オプションで指定されるエラーログのパスを取得してみてください。 このオプションとパスは、Roslyn アナライザー ビルド タスクのログに示されます。 たとえば、 **/errorlog:F:\ts-services-123\_work\456\s\Some\Project\Code\Code.csproj.sarif** のようになります

##### <a name="the-c-compiler-version-isnt-recent-enough"></a>C# コンパイラのバージョンが最新ではありません

最新バージョンの C# コンパイラを入手するには、[Microsoft.Net.Compilers](https://www.nuget.org/packages/Microsoft.Net.Compilers) にアクセスします。 インストールされているバージョンを取得するには、コマンド プロンプトで **csc.exe /version** を実行します。 バージョン 2.6 以降の Microsoft.Net.Compilers NuGet パッケージを参照していることを確認します。

##### <a name="msbuild-and-vsbuild-logs-arent-found"></a>MSBuild と VSBuild のログが見つかりません

Roslyn アナライザー ビルド タスクでは、MSBuild ビルド タスクの MSBuild ログについて Azure DevOps にクエリを実行する必要があります。 MSBuild タスクの直後にアナライザー タスクを実行した場合、ログはまだ使用できません。 MSBuild タスクと Roslyn アナライザー タスクの間に他のタスクを配置します。 その他のタスクの例としては、BinSkim と Anti-Malware Scanner があります。

## <a name="next-steps"></a>次の手順

さらにサポートが必要な場合は、Microsoft Security Code Analysis サポートまでお問い合わせください (平日の午前 9:00 から午後 5:00 (太平洋標準時) まで営業)。

  - オンボード:使用開始手順については、担当のテクニカル アカウント マネージャーにお問い合わせください。
  
  - サポート:[Microsoft Security Code Analysis サポート](mailto:mscahelp@microsoft.com?Subject=Microsoft%20Security%20Code%20Analysis%20Support%20Request)までメールをお送りください。

  >[!NOTE] 
  >Microsoft の有料サポートに申し込まれていない可能性があります。 または、Phoenix カタログからサービスを購入できないサポート オファリングをご利用の場合があります。 これらの条件のいずれかに該当する場合は、[サポート サービスのホーム ページ](https://www.microsoft.com/enterprise/services/support)で詳細を確認してください。
