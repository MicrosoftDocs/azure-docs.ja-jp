---
title: Microsoft Azure Security Code Analysis タスク カスタマイズ ガイド
description: この記事では、Microsoft Security Code Analysis 拡張機能のタスクのカスタマイズについて説明します
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
ms.openlocfilehash: c0d49c3ce06f6fa72daf7aff466ef65e09ced09a
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241812"
---
# <a name="configure-and-customize-the-build-tasks"></a>ビルド タスクを構成およびカスタマイズする

この記事では、各ビルド タスクで使用できる構成オプションについて詳しく説明します。 この記事では、最初にセキュリティ コード分析ツールのタスクについて説明します。 最後に、後処理タスクについて説明します。

## <a name="anti-malware-scanner-task"></a>Anti-Malware Scanner タスク

>[!NOTE]
> Anti-Malware Scanner ビルド タスクには、Windows Defender が有効なビルド エージェントが必要です。 ホストされている Visual Studio 2017 以降には、このようなエージェントが用意されています。 ビルド タスクは、Visual Studio 2015 ホステッド エージェントでは実行されません。
>
> これらのエージェントでは署名を更新することはできませんが、署名の期間を常に 3 時間未満にする必要があります。

タスクの構成の詳細については、次のスクリーンショットと説明を参照してください。

![Anti-Malware Scanner ビルド タスクの構成](./media/security-tools/5-add-anti-malware-task600.png)

スクリーンショットの **[Type]\(種類\)** ボックスの一覧では、 **[Basic]\(基本\)** が選択されています。 スキャンをカスタマイズするコマンドライン引数を指定するには、 **[Custom]\(カスタム\)** を選択します。

Windows Defender は、Windows Update クライアントを使用してシグネチャをダウンロードし、インストールします。 ビルド エージェントでシグネチャの更新に失敗した場合、Windows Update から **HRESULT** エラー コードが返される可能性があります。

Windows Update のエラーとその軽減策について詳しくは、「[コンポーネント別の Windows Update エラーコード](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference)」および TechNet の記事「[Windows Update エージェント - エラーコード](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx)」をご覧ください。

## <a name="binskim-task"></a>BinSkim タスク

> [!NOTE]
> BinSkim タスクを実行するには、ビルドで次のいずれかの条件が事前に満たされている必要があります。
>    - ビルドによってマネージド コードからバイナリ成果物が生成される。
>    - BinSkim で分析するバイナリ成果物がコミット済みである。

タスクの構成の詳細については、次のスクリーンショットとリストを参照してください。

![BinSkim ビルド タスクの構成](./media/security-tools/7-binskim-task-details.png)

- .pdb デバッグ ファイルが生成されるように、ビルド構成をデバッグに設定します。 BinSkim では、これらのファイルを使用して、出力バイナリの問題がソース コードにマップされます。
- 独自のコマンド ラインが調査および作成されないようにするため:
     - **[Type]\(種類\)** ボックスの一覧で、 **[Basic]\(基本\)** を選択します。
     - **[Function]\(機能\)** ボックスの一覧で、 **[Analyze]\(分析\)** を選択します。
- **[Target]\(ターゲット\)** に、ファイル、ディレクトリ、またはフィルター パターンの指定子を 1 つ以上入力します。 これらの指定子は、分析対象の 1 つ以上のバイナリに解決されます。
    - 複数のターゲットを指定するには、セミコロン (;) で区切る必要があります。
    - 指定子は、単一のファイルでも、ワイルドカードが含まれていてもかまいません。
    - ディレクトリの指定は、常に \\* で終わる必要があります。
    - 次に例を示します。

           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;

- **[Type]\(種類\)** ボックスの一覧で **[Command Line]\(コマンド ライン\)** を選択した場合は、binskim.exe を実行する必要があります。
     - binskim.exe に対するの最初の引数では、動詞 **analyze** に続けて 1 つ以上のパスを指定する必要があります。 各パスには、完全なパスまたはソース ディレクトリを基準とした相対パスを指定できます。
     - 複数のターゲット パスは、スペースで区切る必要があります。
     - **/o** または **/output** オプションは省略できます。 出力の値は自動的に追加または置換されます。
     - 標準のコマンドライン構成は次のようになります。

           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose

          > [!NOTE]
          > ターゲットに対してディレクトリを指定する場合、末尾の \\* は重要です。

BinSkim のコマンドライン引数、ID ごとのルール、終了コードについて詳しくは、[BinSkim のユーザー ガイド](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md)を参照してください。

## <a name="credential-scanner-task"></a>Credential Scanner タスク

タスクの構成の詳細については、次のスクリーンショットとリストを参照してください。

![Credential Scanner ビルド タスクの構成](./media/security-tools/3-taskdetails.png)

利用可能なオプションは、次のとおりです。

  - **[Output Format]\(出力形式\)** : 使用可能な値は、 **[TSV]** 、 **[CSV]** 、 **[SARIF]** 、 **[PREfast]** などです。
  - **[Tool Version]\(ツールのバージョン\)** : **[Latest]\(最新\)** を選択することをお勧めします。
  - **[Scan Folder]\(スキャン フォルダー\)** : スキャン対象のリポジトリ フォルダーです。
  - **[Searchers File Type]\(サーチャー ファイルの種類\)** : スキャンに使用されるサーチャー ファイルを探すためのオプションです。
  - **[Suppressions File]\(抑制ファイル\)** : [JSON](https://json.org/) ファイルでは、出力ログの問題を抑制できます。 抑制のシナリオについて詳しくは、この記事の FAQ セクションをご覧ください。
  - **[Verbose Output]\(詳細出力\)** : 名前のとおりです。
  - **バッチ サイズ**:Credential Scanner の実行に使用する同時実行スレッドの数です。 既定値は 20 です。 指定できる値は 1 から 2,147,483,647 までです。
  - **[Match Timeout]\(照合タイムアウト\)** : サーチャーがチェックを中止する前に照合に費やす時間です (秒単位)。
  - **[File Scan Read Buffer Size]\(ファイル スキャン読み取りバッファー サイズ\)** サイズ: コンテンツの読み取り中に使用されるバッファーのバイト サイズです。 既定値は 524,288 です。  
  - **[Maximum File Scan Read Bytes]\(ファイル スキャン最大読み取りバイト数\)** : コンテンツの分析中にファイルから読み取る最大バイト数です。 既定値は 104,857,600 です。
  - **[Control Options]\(制御オプション\)**  >  **[Run this task]\(このタスクを実行する\)** : タスクをいつ実行するかを指定します。 さらに複雑な条件を指定するには、 **[Custom conditions]\(カスタム条件\)** を選択します。
  - **バージョン**:Azure DevOps 内でのビルド タスクのバージョンです。 このオプションを使用することはあまりありません。

## <a name="microsoft-security-risk-detection-task"></a>Microsoft Security Risk Detection タスク

> [!NOTE]
> Microsoft Security Risk Detection (MSRD) タスクを使用する前に、MSRD サービスでアカウントを作成して構成する必要があります。 このサービスは、個別のオンボード プロセスを必要とします。 この拡張機能の他のほとんどのタスクとは異なり、このタスクには MSRD での別のサブスクリプションが必要です。
>
> その手順については、[Microsoft Security Risk Detection](https://aka.ms/msrddocs) に関するページと [Microsoft Security Risk Detection の使い方](https://docs.microsoft.com/security-risk-detection/how-to/)に関するページを参照してください。

次の一覧ではこのタスクの構成の詳細を示します。 UI 要素については、その要素の上にマウスポインターを置くとヘルプが表示されます。

   - **[Azure DevOps Service Endpoint Name for MSRD]\(MSRD 用 Azure DevOps サービス エンドポイント名\)** : Azure DevOps サービス エンドポイントのジェネリック型には、オンボードされた MSRD インスタンスの URL と REST API アクセス トークンが格納されます。 そのようなエンドポイントを作成してある場合は、ここで指定できます。 それ以外の場合は、 **[Manage]\(管理\)** リンクを選択して、この MSRD タスク用の新しいサービス エンドポイントを作成して構成します。
   - **[アカウント ID]** : MSRD アカウントの URL から取得できる GUID です。
   - **[URLs to Binaries]\(バイナリの URL\)** : パブリックに使用できる URL のセミコロン区切りリストです。 ファジー テスト マシンでは、これらの URL を使用してバイナリがダウンロードされます。
   - **[URLs of the Seed Files]\(シード ファイルの URL\)** : パブリックに使用できる URL のセミコロン区切りリストです。 ファジー テスト マシンでは、これらの URL を使用してシードがダウンロードされます。 シード ファイルがバイナリと一緒にダウンロードされる場合、この値の指定は省略可能です。
   - **[OS Platform Type]\(OS プラットフォーム タイプ\)** : ファジー テスト ジョブが実行されるマシンのオペレーティング システム (OS) プラットフォームです。 使用できる値は、**Windows** と **Linux** です。
   - **[Windows Edition / Linux Edition]\(Windows エディション/Linux エディション\)** : ファジー テスト ジョブが実行されるマシンの OS エディションです。 マシンの OS エディションが既定値と異なる場合は、既定値を上書きできます。
   - **[Package Installation Script]\(パッケージ インストール スクリプト\)** : テスト マシンで実行するスクリプトです。 このスクリプトでは、ファジー テスト ジョブが送信される前に、テスト ターゲット プログラムとその依存関係がインストールされます。
   - **[Job Submission Parameters]\(ジョブ送信パラメーター\)** :
       - **[Seed Directory]\(シード ディレクトリ\)** : シードが格納されるファジー テスト マシン上のディレクトリのパスです。
       - **[Seed Extension]\(シード拡張子\)** : シードのファイル名拡張子です。
       - **[Test Driver Executable]\(テスト ドライバー実行可能ファイル\)** : ファジー テスト マシン上のターゲット実行可能ファイルへのパスです。
       - **[Test Driver Executable Architecture]\(テスト ドライバー実行可能ファイル アーキテクチャ\)** : ターゲット実行可能ファイルのアーキテクチャです。 使用できる値は **x86** と **amd64** です。
       - **[Test Driver Arguments]\(テスト ドライバー引数\)** : テスト実行可能ファイルに渡されるコマンド ライン引数です。 引数 "%testfile%" (引用符を含む) は、ターゲット ファイルへの完全パスに自動的に置き換えられます。 このファイルは、テスト ドライバーによって解析され、必須です。
       - **[Test Driver Process Exits Upon Test Completion]\(テストの完了時にテスト ドライバー プロセスを終了する\)** : 完了時にテスト ドライバーを終了する場合は、このチェック ボックスをオンにします。 テスト ドライバーを強制的に閉じる必要がある場合はオフにします。
       - **[Maximum Duration (in seconds)]\(最大継続期間 (秒)\)** : ターゲット プログラムで入力ファイルを解析するのに必要であると合理的に考えられる最大推定時間です。 この推定の精度が高いほど、ファジー テスト アプリの実行効率が上がります。
       - **[Test Driver Can Be Run Repeatedly]\(テスト ドライバーは繰り返し実行可能\)** : 永続化または共有されたグローバルな状態に依存することなくテスト ドライバーを繰り返し実行できる場合は、このチェック ボックスをオンにします。
       - **[Test Driver Can Be Renamed]\(テスト ドライバーは名前の変更が可能\)** : テスト ドライバー実行可能ファイルの名前を変更しても正しく動作する場合は、このチェック ボックスをオンにします。
       - **[The Fuzzing Application Runs as a Single OS Process]\(ファジー テスト アプリケーションが単一の OS プロセスとして動作する\)** : テスト ドライバーが 1 つの OS プロセスで実行される場合は、このチェック ボックスをオンにします。 テスト ドライバーによって追加のプロセスが生成される場合は、オフにします。

## <a name="roslyn-analyzers-task"></a>Roslyn Analyzers タスク

> [!NOTE]
> Roslyn Analyzers タスクを実行する前に、ビルドで次の条件が満たされている必要があります。
> - C# または Visual Basic のコードをコンパイルするための組み込みの MSBuild または VSBuild ビルド タスクがビルド定義に含まれる。 アナライザー タスクは、Roslyn アナライザーが有効な状態で MSBuild コンパイルを再実行するために、組み込みタスクの入力と出力に依存します。
> - コンパイラ バージョン 2.6 以降が使用されるように、このビルド タスクを実行するビルド エージェントに、Visual Studio 2017 バージョン 15.5 以降がインストールされている。

タスクの構成の詳細については、次のリストと注意を参照してください。

利用可能なオプションは、次のとおりです。

- **[Ruleset]\(ルールセット\)** : 値は、 **[SDL Required]\(SDL が必須\)** 、 **[SDL Recommended]\(SDL が推奨\)** 、または独自のカスタム ルールセットです。
- **[Analyzers Version]\(アナライザーのバージョン\)** : **[Latest]\(最新\)** を選択することをお勧めします。
- **[Compiler Warnings Suppressions File]\(コンパイラ警告抑制ファイル\)** : 抑制する警告 ID のリストが含まれるテキスト ファイルです。
- **[Control Options]\(制御オプション\)**  >  **[Run this task]\(このタスクを実行する\)** : タスクをいつ実行するかを指定します。 さらに複雑な条件を指定するには、 **[Custom conditions]\(カスタム条件\)** を選択します。

> [!NOTE]
> - Roslyn アナライザーはコンパイラに統合されており、csc.exe コンパイルの一部としてのみ実行できます。 したがって、このタスクでは、ビルドで既に実行されたコンパイラ コマンドを再生または再度実行する必要があります。 この再生または実行は、Visual Studio Team Services (VSTS) に対して MSBuild ビルド タスクのログのクエリを実行することによって行われます。
>
>   タスクでビルド定義から MSBuild コンパイル コマンド ラインを確実に取得する方法は、他にはありません。 ユーザーがコマンド ラインを入力できるように、自由形式のテキスト ボックスを追加することが検討されました。 しかし、それらのコマンド ラインを最新の状態に保ち、メイン ビルドと同期させるのは困難です。
>
>   カスタム ビルドでは、コンパイラ コマンドだけでなく、コマンドのセット全体を再生する必要があります。 このような場合、Roslyn アナライザーを有効にすることは、簡単ではなく信頼できません。
>
> - Roslyn アナライザーは、コンパイラと統合されています。 呼び出すには、Roslyn アナライザーをコンパイルする必要があります。
>
>   この新しいビルド タスクは、既にビルドされている C# プロジェクトを再コンパイルすることによって実装されます。 新しいタスクでは、元のタスクと同じビルドまたはビルド定義に含まれる MSBuild および VSBuild のビルド タスクのみが使用されます。 しかし、この場合、新しいタスクでは Roslyn アナライザーが有効になっています。
>
>   新しいタスクが元のタスクと同じエージェント上で実行される場合、新しいタスクの出力によって、*s* ソース フォルダー内の元のタスクの出力が上書きされます。 ビルド出力は同じものですが、MSBuild を実行し、出力を成果物のステージング ディレクトリにコピーしてから、Roslyn アナライザーを実行することをお勧めします。

Roslyn Analyzers タスクに関するその他のリソースについては、Microsoft Docs の「[Roslyn ベースのアナライザー](https://docs.microsoft.com/dotnet/standard/analyzers/)」を参照してください。

このビルド タスクによってインストールされて使用されるアナライザー パッケージは、NuGet の [Microsoft.CodeAnalysis.FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers) ページで確認できます。

## <a name="tslint-task"></a>TSLint タスク

TSLint の詳細については、[GitHub の TSLint リポジトリ](https://github.com/palantir/tslint)を参照してください。

>[!NOTE] 
>ご存知かもしれませんが、[GitHub の TSLint リポジトリ](https://github.com/palantir/tslint)のホーム ページでは、2019 年に TSLint が非推奨になることが示されています。 Microsoft では、代わりのタスクとして [ESLint](https://github.com/eslint/eslint) を調査しています。

## <a name="publish-security-analysis-logs-task"></a>Publish Security Analysis Logs タスク

タスクの構成の詳細については、次のスクリーンショットとリストを参照してください。

![Publish Security Analysis Logs ビルド タスクの構成](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **[Artifact Name]\(成果物の名前\)** : 任意の文字列識別子です。
- **[Artifact Type]\(成果物の種類\)** : 選択に応じて、Azure DevOps サーバー、またはビルド エージェントからアクセスできる共有ファイルに、ログを発行できます。
- **[ツール]** :特定のツールのログを保持することも、 **[All Tools]\(すべてのツール\)** を選択してすべてのログを保持することもできます。

## <a name="security-report-task"></a>Security Report タスク

Security Report の構成の詳細については、次のスクリーンショットとリストを参照してください。

![Security Report ビルドタスクの構成](./media/security-tools/4-createsecurityanalysisreport600.png)

- **[Reports]\(レポート\)** : **[Pipeline Console]\(パイプライン コンソール\)** 、 **[TSV File]\(TSV ファイル\)** 、 **[Html File]\(Html ファイル\)** 形式のいずれかを選択します。 選択した形式ごとに 1 つのレポート ファイルが作成されます。
- **[ツール]** :検出された問題の要約が必要なビルド定義内のツールを選択します。 選択した各ツールには、要約レポートにエラーのみを表示するか、エラーと警告の両方を表示するかを選択するためのオプションが存在する場合があります。
- **[Advanced Options]\(詳細オプション\)** : 選択したツールのいずれかにログが存在しない場合、警告またはエラーをログに記録することを選択できます。 エラーをログに記録すると、タスクは失敗します。
- **[Base Logs Folder]\(基本ログフォルダー\)** : ログの検索先となる基本ログ フォルダーをカスタマイズできます。 ただし、このオプションは通常は使用されません。

## <a name="post-analysis-task"></a>Post-Analysis タスク

タスクの構成の詳細については、次のスクリーンショットとリストを参照してください。

![Post-Analysis ビルド タスクの構成](./media/security-tools/a-post-analysis600.png)

- **[ツール]** :条件に従ってビルドの中断を挿入するビルド定義内のツールを選択します。 選択した各ツールには、エラーでのみ中断するか、エラーと警告の両方を表示するかを選択するためのオプションが存在する場合があります。
- **[Report]\(レポート\)** : 必要に応じて、ビルド中断の原因になっている結果を書き込むことができます。 結果は、Azure DevOps コンソール ウィンドウとログ ファイルに書き込まれます。
- **[Advanced Options]\(詳細オプション\)** : 選択したツールのいずれかにログが存在しない場合、警告またはエラーをログに記録することを選択できます。 エラーをログに記録すると、タスクは失敗します。

## <a name="next-steps"></a>次の手順

Security Code Analysis 拡張機能と提供されるツールについてさらに質問がある場合は、[FAQ ページ](security-code-analysis-faq.md)を参照してください。
