---
title: Visual Studio テンプレートを使用してソリューションを構築する - Azure Batch | Microsoft Docs
description: Visual Studio プロジェクト テンプレートを使用して、多くのコンピューティング処理を要するワークロードを Azure Batch 上に実装して実行する方法について説明します。
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 5e041ae2-25af-4882-a79e-3aa63c4bfb20
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 60662e723a55c969fdd4b70e732303c90bbf9e8b
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094340"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Visual Studio プロジェクト テンプレートを使用して Batch ソリューションをすぐに開始する

Batch 用の Visual Studio テンプレート (**ジョブ マネージャー**と**タスク プロセッサ**) に用意されているコードを使用すると、多くのコンピューティング処理を要するワークロードを最小限の手間で Batch 上に実装し、実行することができます。 このドキュメントでは、これらのテンプレートについて説明すると共に、その使用方法についての指針を示しています。

> [!IMPORTANT]
> この記事の内容は、あくまで上記 2 つのテンプレートについて記述したものであり、Batch サービスとその主要概念 (プール、コンピューティング ノード、ジョブ/タスク、ジョブ マネージャー タスク、環境変数などの関連する情報) に精通した読者を想定しています。 詳細については、「[Azure Batch とは](batch-technical-overview.md)」と[開発者向けの Batch 機能の概要](batch-api-basics.md)に関するページを参照してください。
> 
> 

## <a name="high-level-overview"></a>概要
ジョブ マネージャー テンプレートとタスク プロセッサ テンプレートを使用すると、次に示した 2 つの実用的なコンポーネントを作成することができます。

* ジョブ スプリッターを実装するジョブ マネージャー タスク。ジョブ スプリッターは、並列実行可能な独立した複数のタスクにジョブを分割します。
* アプリケーション コマンド ラインの前処理と後処理を実行するためのタスク プロセッサ。

たとえばムービーをレンダリングする状況を考えてみましょう。この場合、ジョブ スプリッターによって 1 つのムービー ジョブが、各フレームを個別に処理する数百から数千の独立したタスクに分割されます。 同様に、レンダリング アプリケーションとさまざまな依存プロセス (それぞれのフレームをレンダリングするために必要なプロセス)、その他必要な操作 (レンダリング済みのフレームをストレージにコピーするなど) が、タスク プロセッサによって呼び出されます。

> [!NOTE]
> ジョブ マネージャー テンプレートとタスク プロセッサ テンプレートは互いに独立しているので、両方を使用するか、どちらか一方のみを使用するかは、コンピューティング ジョブの要件と各自の事情によって自由に選ぶことができます。
> 
> 

以下の図に示したように、これらのテンプレートを使用するコンピューティング ジョブには、次の 3 つの段階があります。

1. クライアント コード (アプリケーション、Web サービスなど) が、Azure 上の Batch サービスにジョブを送信します。このとき、そのジョブ マネージャー タスクとしてジョブ マネージャー プログラムが指定されます。
2. Batch サービスが、コンピューティング ノード上でジョブ マネージャー タスクを実行します。ジョブ スプリッターは、そのコードが受け取ったパラメーターと指定に基づき、必要に応じた数のコンピューティング ノード上で、指定された数のタスク プロセッサ タスクを開始します。
3. 個々のタスク プロセッサ タスクが並列実行され、入力データが処理されて、出力データが生成されます。

![Diagram showing how client code interacts with the Batch service][diagram01]

## <a name="prerequisites"></a>前提条件
これらの Batch テンプレートを使用するための前提条件は次のとおりです。

* Visual Studio 2015 がインストールされているコンピューター。 バッチのテンプレートは現在、Visual Studio 2015 でのみサポートされています。
* Batch テンプレート。[Visual Studio ギャラリー][vs_gallery]から Visual Studio の拡張機能として入手できます。 これらのテンプレートは、次の 2 とおりの方法で入手できます。
  
  * Visual Studio の **[拡張機能と更新プログラム]** ダイアログ ボックスを使用してテンプレートをインストールする (「[Visual Studio 拡張機能の検索と使用][vs_find_use_ext]」を参照)。 **[拡張機能と更新プログラム]** ダイアログ ボックスで、次の 2 つの拡張機能を検索してダウンロードします。
    
    * Azure Batch Job Manager with Job Splitter (Azure Batch ジョブ マネージャーとジョブ スプリッター)
    * Azure Batch Task Processor (Azure Batch タスク プロセッサ)
  * Visual Studio のオンライン ギャラリーから[Microsoft Azure Batch プロジェクト テンプレート][vs_gallery_templates]をダウンロードする
* [アプリケーション パッケージ](batch-application-packages.md) 機能を使用してジョブ マネージャーとタスク プロセッサを Batch コンピューティング ノードにデプロイする予定がある場合は、ご利用の Batch アカウントにストレージ アカウントを関連付ける必要があります。

## <a name="preparation"></a>準備
ソリューションを 1 つ作成してそこにジョブ マネージャーとタスク プロセッサを含めることをお勧めします。そうすることで、ジョブ マネージャーとタスク プロセッサのプログラム間でコードを共有しやすくなります。 このソリューションの作成手順は次のとおりです。

1. Visual Studio を開き、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。
2. **[テンプレート]** の **[その他のプロジェクトの種類]** を展開し、 **[Visual Studio ソリューション]** をクリックして **[空のソリューション]** を選択します。
3. アプリケーションの内容とこのソリューションの目的とを表す名前を入力します (例: "LitwareBatchTaskPrograms")。
4. 新しいソリューションを作成する場合は **[OK]** をクリックします。

## <a name="job-manager-template"></a>ジョブ マネージャー テンプレート
ジョブ マネージャー テンプレートを通じて実装されるジョブ マネージャー タスクによって、次の操作を実行できます。

* ジョブを複数のタスクに分割する。
* 分割されたタスクを送信して Batch 上で実行する。

> [!NOTE]
> ジョブ マネージャー タスクについて詳しくは、「 [開発者向け Batch 機能の概要](batch-api-basics.md#job-manager-task)」をご覧ください。
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>テンプレートによるジョブ マネージャーの作成
先ほど作成したソリューションにジョブ マネージャーを追加するには、次の手順を実行します。

1. 既にあるソリューションを Visual Studio で開きます。
2. ソリューション エクスプローラーでソリューションを右クリックし、 **[追加]**  >  **[新しいプロジェクト]** の順にクリックします。
3. **[Visual C#]** の **[クラウド]** をクリックし、 **[Azure Batch Job Manager with Job Splitter (Azure Batch ジョブ マネージャーとジョブ スプリッター)]** をクリックします。
4. アプリケーションを表す名前を入力し、このプロジェクトをジョブ マネージャーとして指定します (例: "LitwareJobManager")。
5. プロジェクトを作成する場合は **[OK]** をクリックします。
6. 最後に、プロジェクトをビルドします。プロジェクトの編集を開始する前に、参照設定されているすべての NuGet パッケージを Visual Studio で強制的に読み込んで、プロジェクトが有効であることを確認してください。

### <a name="job-manager-template-files-and-their-purpose"></a>ジョブ マネージャー テンプレート ファイルとその目的
ジョブ マネージャー テンプレートを使用してプロジェクトを作成すると、次の 3 種類のコード ファイルが生成されます。

* メイン プログラム ファイル (Program.cs)。 このファイルには、プログラムのエントリ ポイントと最上位の例外処理が含まれています。 通常、このファイルへの変更は必要ありません。
* Framework ディレクトリ。 ジョブ マネージャー プログラムによって実行される "定型的" な処理 (パラメーターのアンパック、Batch ジョブへのタスクの追加など) を担うファイルが格納されます。通常、これらのファイルへの変更は必要ありません。
* ジョブ スプリッター ファイル (JobSplitter.cs)。 ジョブをタスクに分割するためのアプリケーション固有のロジックは、このファイルに記述します。

実際のジョブ スプリッター コードに関連して必要なファイルは当然、ジョブの分割ロジックの複雑さに応じて適宜追加できます。

このテンプレートからは他にも、.NET の標準的なプロジェクト ファイルが生成されます (.csproj ファイル、app.config、packages.config など)。

このセクションの残りの部分では、各種のファイルとそのコード構造、各クラスの役割について説明します。

![Visual Studio Solution Explorer showing the Job Manager template solution][solution_explorer01]

**Framework ディレクトリ内のファイル**

* `Configuration.cs`:ジョブの構成データ (Batch アカウントの詳細、リンクされたストレージ アカウントの資格情報、ジョブ/タスク情報、ジョブのパラメーターなど) の読み込みがカプセル化されています。 また、Batch で定義されている環境変数には、このファイルの Configuration.EnvironmentVariable クラスを通じてアクセスすることができます (Batch のドキュメントでタスクの環境設定に関するページを参照)。
* `IConfiguration.cs`:Configuration クラスの実装を抽象化します。ダミーの構成オブジェクトを使用して、実際のジョブ スプリッターの単体テストを実行することができます。
* `JobManager.cs`:ジョブ マネージャー プログラムのコンポーネントを指揮します。 ジョブ スプリッターの初期化と呼び出しのほか、ジョブ スプリッターから返されたタスクを TaskSubmitter にディスパッチする役割を果たします。
* `JobManagerException.cs`:ジョブ マネージャーの強制終了につながるエラーを表します。 JobManagerException は、強制終了の過程で具体的な診断情報を出力する "予期" されたエラーをラップするために使用されます。
* `TaskSubmitter.cs`:このクラスは、ジョブ スプリッターから返されたタスクを Batch ジョブに追加する役割を果たします。 JobManager クラスは、タスクを効率的かつタイミングよくジョブに追加できるように、一連のタスクをバッチ単位に集約し、そのバッチごとに、バックグラウンド スレッドで TaskSubmitter.SubmitTasks を呼び出します。

**ジョブ スプリッター**

`JobSplitter.cs`:このクラスには、ジョブをタスクに分割するためのアプリケーション固有のロジックが記述されています。 ジョブ マネージャー フレームワークは JobSplitter.Split メソッドを呼び出して一連のタスクを取得します。Batch ジョブにはそれらのタスクが、メソッドから返されるタイミングで追加されます。 実際のジョブのロジックは、このクラスに追加することになります。 ジョブの分割後のタスクを表す一連の CloudTask インスタンスを返すように Split メソッドを実装してください。

**.NET の標準的なコマンド ライン プロジェクト ファイル**

* `App.config`:.NET の標準的なアプリケーション構成ファイル。
* `Packages.config`:NuGet パッケージの標準的な依存関係ファイル。
* `Program.cs`:プログラムのエントリ ポイントと最上位の例外処理が含まれています。

### <a name="implementing-the-job-splitter"></a>ジョブ スプリッターの実装
ジョブ マネージャー テンプレート プロジェクトを開いたとき、初期設定では JobSplitter.cs ファイルが表示されるようになっています。 実際のワークロードのタスクに関する分割ロジックは、以下に示した Split() メソッドを使って実装できます。

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> ジョブ マネージャー テンプレートのコードの中で利用者による編集が想定されているのは、`Split()` メソッド内のコメント化されたセクションだけです。ジョブを複数のタスクに分割するためのロジックをここに追加します。 別のテンプレート セクションを編集する場合は、Batch の動作に関する十分な理解が必要です。[Batch 関連のコード サンプル][github_samples]をいくつか体験してみてください。
> 
> 

Split() の実装コードからは次のデータにアクセスできます。

* ジョブのパラメーター。`_parameters` フィールドを通じてアクセスします。
* ジョブを表す CloudJob オブジェクト。`_job` フィールドを通じてアクセスします。
* ジョブ マネージャー タスクを表す CloudTask オブジェクト。`_jobManagerTask` フィールドを通じてアクセスします。

`Split()` の実装コードで直接ジョブにタスクを追加する必要はありません。 実装コードの戻り値が一連の CloudTask オブジェクトになっていれば、ジョブ スプリッターを呼び出すジョブ マネージャー フレームワーク クラスによって、それらのオブジェクトがジョブに自動的に追加されます。 ジョブ スプリッターは、C# の反復子 (`yield return`) 機能を使って実装するのが一般的です。この機能を使うことで、すべてのタスクの計算が終わるのを待たずにすぐタスクの実行を開始することができます。

**ジョブ スプリッターのエラー**

ジョブ スプリッターでエラーが発生した場合、次のいずれかの措置が必要となります。

* C# の `yield break` ステートメントで反復処理を終了する。この場合、ジョブ マネージャーは実行に成功したものと見なされます。
* 例外をスローする。この場合、ジョブ マネージャーは実行に失敗したものと見なされます (クライアントの構成によっては、再試行される場合もあります)。

どちらの場合も、既にジョブ スプリッターから返されて Batch ジョブに追加されたタスクについては、実行の候補となります。 そのようなタスクが実行されないようにしたい場合は、次の方法が考えられます。

* ジョブ スプリッターからタスクが返される前にジョブを強制終了する。
* タスク コレクション全体を構築したうえで返す (つまり、C# の反復子を使ってジョブ スプリッターを実装するのではなく `ICollection<CloudTask>` どちらか `IList<CloudTask>` instead of implementing your job splitter using a C# iteratどちらか)
* タスクの依存関係を使って、ジョブ マネージャーの正常完了をすべてのタスクの依存条件とする。

**ジョブ マネージャーの再試行**

ジョブ マネージャーは、エラーが発生した場合、クライアントの再試行設定によっては、Batch サービスによって再試行されます。 ジョブ マネージャー フレームワークが Batch ジョブにタスクを追加する際、既に存在するタスクは無視されるので、それによって問題が生じることは通常はありません。 しかし、タスクの計算負荷が大きい場合、既に Batch ジョブに追加されているタスクの再計算に伴うコストは避けたいと考えるのが普通でしょう。そもそも、再実行で同じタスク ID が生成される保証がなければ、"既存タスクを無視" する機能は働きません。 このような場合は、タスクの生成を開始する前に CloudJob.ListTasks を実行するなどして、既に完了している作業を検出し、再実行されないようにジョブ スプリッターを設計することをお勧めします。

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>ジョブ マネージャー テンプレートの終了コードと例外
終了コードと例外は、プログラムの実行結果を判定する機構であり、プログラムの実行時に発生した問題の特定に役立てられます。 ジョブ マネージャー テンプレートには、このセクションで説明されている終了コードと例外が実装されています。

ジョブ マネージャー テンプレートによって実装されたジョブ マネージャー タスクから返される可能性がある終了コードは次の 3 つです。

| コード | 説明 |
| --- | --- |
| 0 |ジョブ マネージャーは正常終了しました。 ジョブ スプリッター コードは最後まで実行され、すべてのタスクが Batch ジョブに追加されました。 |
| 1 |"予期" されていた箇所のプログラム コードで例外が発生してジョブ マネージャー タスクが失敗しました。 この例外は JobManagerException に変換されます。その際、診断情報のほか、可能であればエラーを解決するための推奨情報が出力されます。 |
| 2 |ジョブ マネージャー タスクは、"予期しない" 例外で失敗しました。 標準出力に例外が記録されましたが、診断や解決方法に関する情報をジョブ マネージャーは追加できませんでした。 |

ジョブ マネージャー タスクが失敗しても、一部のタスクはエラーの発生前に既に追加されている可能性があります。 これらのタスクは、通常どおり実行されます。 このコード パスについて詳しくは、「ジョブ スプリッターのエラー」をご覧ください。

例外によって返されたすべての情報は、stdout.txt ファイルと stderr.txt ファイルに書き込まれます。 詳しくは、「 [エラー処理](batch-api-basics.md#error-handling)」をご覧ください。

### <a name="client-considerations"></a>クライアントの考慮事項
ここでは、クライアントの実装に関して、このテンプレートに基づいてジョブ マネージャーを呼び出す際のいくつかの要件について説明します。 パラメーターと環境設定の受け渡しについて詳しくは、 [クライアント コードからパラメーターと環境変数を渡す方法](#pass-environment-settings) をご覧ください。

**必須の資格情報**

ジョブ マネージャー タスクで Azure Batch ジョブにタスクを追加するためには、ご使用の Azure Batch アカウントの URL とキーが必要となります。 これらの情報は、YOUR_BATCH_URL と YOUR_BATCH_KEY という環境変数で渡す必要があります。 これらの変数の設定は、ジョブ マネージャー タスクの環境設定で行うことができます。 たとえば、C# クライアントで次のように記述します。

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**ストレージの資格情報**

通常、リンクされたストレージ アカウントの資格情報をクライアントからジョブ マネージャー タスクに渡す必要はありません。なぜなら、(a) ほとんどのジョブ マネージャーは、リンクされたストレージ アカウントに明示的にアクセスする必要がなく、(b) リンクされたストレージ アカウントは通常、ジョブの共通の環境設定としてすべてのタスクに提供されることが多いためです。 リンクされたストレージ アカウントを共通の環境設定で指定しておらず、かつリンクされたストレージにジョブ マネージャーがアクセスしなければならない場合は、次のようにして、リンクされたストレージの資格情報を指定してください。

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**ジョブ マネージャー タスクの設定**

ジョブ マネージャーの *killJobOnCompletion* フラグは、クライアントで **false**に設定する必要があります。

通常、 *runExclusive* は、クライアントで **false**に設定することをお勧めします。

ジョブ マネージャーの実行可能ファイル (とその必須 DLL) は、クライアントが *resourceFiles* または *applicationPackageReferences* コレクションを使用してコンピューティング ノードにデプロイする必要があります。

ジョブ マネージャーでエラーが発生しても、既定では再試行されません。 ジョブ マネージャーのロジックに応じて、*constraints*/*maxTaskRetryCount* を使ってクライアント側で再試行を有効にしてください。

**ジョブの設定**

依存関係を持ったタスクがジョブ スプリッターから出力された場合、クライアントでジョブの usesTaskDependencies を true に設定する必要があります。

ジョブ スプリッター モデルでは、ジョブ スプリッターが作成したタスクに加えて別途、クライアント側でタスクをジョブに追加しなければならないような状況はまれです。 そのため、ジョブの *onAllTasksComplete* は通常、クライアントで **terminatejob**に設定してください。

## <a name="task-processor-template"></a>タスク プロセッサ テンプレート
タスク プロセッサ テンプレートでは、次の操作を実行するタスク プロセッサを実装できます。

* 個々の Batch タスクの実行に必要な情報を設定する。
* 個々の Batch タスクに必要なあらゆる操作を実行する。
* 永続的ストレージにタスク出力を保存する。

Batch でタスクを実行するために必ずしもタスク プロセッサは必要ありません。タスク プロセッサを使用することの主な利点は、そのラッパーを通じて、タスクの実行に伴うあらゆる操作を一箇所で実装できることです。 たとえば、必要に応じて、各タスクのコンテキストで複数のアプリケーションを実行したり、各タスクの完了後にデータを永続的ストレージにコピーしたりすることができます。

タスク プロセッサでは、実際のワークロードに応じて、単純な操作や複雑な操作をいくつでも実行することができます。 加えて、タスクの全操作を 1 つのタスク プロセッサに実装しておけば、アプリケーションに対する変更やワークロード要件の変化に合わせて、操作を更新したり追加したりすることが容易にできます。 ただし、実際の実装においては、タスク プロセッサが最適解とは言えないケースもあります。タスク プロセッサの使用が複雑さを不要に増す場合もあるからです (単純なコマンド ラインからすぐに開始できるジョブを実行するときなど)。

### <a name="create-a-task-processor-using-the-template"></a>テンプレートを使用してタスク プロセッサを作成する
先ほど作成したソリューションにタスク プロセッサを追加するには、次の手順を実行します。

1. 既にあるソリューションを Visual Studio で開きます。
2. ソリューション エクスプローラーで該当ソリューションを右クリックして **[追加]** をクリックし、 **[新しいプロジェクト]** をクリックします。
3. **[Visual C#]** の **[クラウド]** をクリックし、 **[Azure Batch Task Processor (Azure Batch タスク プロセッサ)]** をクリックします。
4. アプリケーションを表す名前を入力し、このプロジェクトをタスク プロセッサとして指定します (例: "LitwareTaskProcessor")。
5. プロジェクトを作成する場合は **[OK]** をクリックします。
6. 最後に、プロジェクトをビルドします。プロジェクトの編集を開始する前に、参照設定されているすべての NuGet パッケージを Visual Studio で強制的に読み込んで、プロジェクトが有効であることを確認してください。

### <a name="task-processor-template-files-and-their-purpose"></a>タスク プロセッサ テンプレート ファイルとその目的
タスク プロセッサ テンプレートを使用してプロジェクトを作成すると、次の 3 種類のコード ファイルが生成されます。

* メイン プログラム ファイル (Program.cs)。 このファイルには、プログラムのエントリ ポイントと最上位の例外処理が含まれています。 通常、このファイルへの変更は必要ありません。
* Framework ディレクトリ。 ジョブ マネージャー プログラムによって実行される "定型的" な処理 (パラメーターのアンパック、Batch ジョブへのタスクの追加など) を担うファイルが格納されます。通常、これらのファイルへの変更は必要ありません。
* タスク プロセッサ ファイル (TaskProcessor.cs)。 タスクを実行するためのアプリケーション固有のロジックは、このファイルに記述します (通常は、既存の実行可能ファイルを呼び出します)。 前処理と後処理のコードもここに記述します (別途必要なデータのダウンロード、結果ファイルのアップロードなど)。

実際のタスク プロセッサ コードに関連して必要なファイルは当然、ジョブの分割ロジックの複雑さに応じて適宜追加できます。

このテンプレートからは他にも、.NET の標準的なプロジェクト ファイルが生成されます (.csproj ファイル、app.config、packages.config など)。

このセクションの残りの部分では、各種のファイルとそのコード構造、各クラスの役割について説明します。

![Visual Studio Solution Explorer showing the Task Processor template solution][solution_explorer02]

**Framework ディレクトリ内のファイル**

* `Configuration.cs`:ジョブの構成データ (Batch アカウントの詳細、リンクされたストレージ アカウントの資格情報、ジョブ/タスク情報、ジョブのパラメーターなど) の読み込みがカプセル化されています。 また、Batch で定義されている環境変数には、このファイルの Configuration.EnvironmentVariable クラスを通じてアクセスすることができます (Batch のドキュメントでタスクの環境設定に関するページを参照)。
* `IConfiguration.cs`:Configuration クラスの実装を抽象化します。ダミーの構成オブジェクトを使用して、実際のジョブ スプリッターの単体テストを実行することができます。
* `TaskProcessorException.cs`:ジョブ マネージャーの強制終了につながるエラーを表します。 TaskProcessorException は、強制終了の過程で具体的な診断情報を出力する "予期" されたエラーをラップするために使用されます。

**タスク プロセッサ**

* `TaskProcessor.cs`:タスクを実行します。 タスク プロセッサ フレームワークによって TaskProcessor.Run メソッドが呼び出されます。 実際のタスクに対するアプリケーション固有のロジックは、このクラスに追加することになります。 Run メソッドを実装して、次の処理を実行します。
  * タスクのパラメーターを解析して検証する
  * 呼び出す外部プログラムのコマンド ラインを作成する
  * デバッグに必要となる診断情報をログに記録する
  * コマンド ラインを使用してプロセスを開始する
  * プロセスの終了を待機する
  * プロセスの終了コードを捕捉して成功か失敗かを判断する
  * 維持したい出力ファイルを永続的ストレージに保存する

**.NET の標準的なコマンド ライン プロジェクト ファイル**

* `App.config`:.NET の標準的なアプリケーション構成ファイル。
* `Packages.config`:NuGet パッケージの標準的な依存関係ファイル。
* `Program.cs`:プログラムのエントリ ポイントと最上位の例外処理が含まれています。

## <a name="implementing-the-task-processor"></a>タスク プロセッサの実装
タスク プロセッサ テンプレート プロジェクトを開いたとき、初期設定では TaskProcessor.cs ファイルが表示されるようになっています。 実際のワークロードのタスクに関する実行ロジックは、以下に示した Run() メソッドを使って実装できます。

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> タスク プロセッサ テンプレートのコードの中で利用者による編集が想定されているのは、Run() メソッド内のコメント化されたセクションだけです。実際のワークロードのタスクに関する実行ロジックをここに追加します。 別のテンプレート セクションを編集する場合は、Batch の動作に関する十分な理解が必要です。まず Batch のドキュメントを読んで、Batch 関連のコード サンプルをいくつか体験してみてください。
> 
> 

コマンド ラインを起動してプロセスを開始し、すべてのプロセスの完了を待って結果を保存した後、最後に終了コードを返す、というのが Run() メソッドの役割です。 実際のタスクの処理ロジックは、Run() メソッドに実装します。 Run() メソッドは、タスク プロセッサ フレームワークによって自動的に呼び出されるので、明示的に呼び出す必要はありません。

Run() の実装コードからは次のデータにアクセスできます。

* タスクのパラメーター。`_parameters` フィールドを通じてアクセスします。
* ジョブ ID とタスク ID。`_jobId` フィールドと `_taskId` フィールドを通じてアクセスします。
* タスクの構成。`_configuration` フィールドを通じてアクセスします。

**タスクの失敗**

エラーが発生した場合、例外をスローして Run() メソッドを終了できますが、その場合、タスクの終了コードを管理している最上位の例外ハンドラーから抜けることになります。 エラーの種類を区別できるようなかたちで終了コードを制御する必要がある場合、たとえば終了コードを使って診断を行うケースや、ジョブを強制終了すべきかどうかがエラーの種類によって異なるケースでは、Run() メソッドを終了するときに 0 以外の終了コードを返すようにしてください。 それがタスクの終了コードになります。

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>タスク プロセッサ テンプレートの終了コードと例外
終了コードと例外は、プログラムの実行結果を判定する機構であり、プログラムの実行時に発生した問題の特定に役立てられます。 タスク プロセッサ テンプレートには、このセクションで説明されている終了コードと例外が実装されています。

タスク プロセッサ テンプレートによって実装されたタスク プロセッサ タスクから返される可能性がある終了コードは次の 3 つです。

| コード | 説明 |
| --- | --- |
| [Process.ExitCode][process_exitcode] |タスク プロセッサは最後まで実行されました。 これは呼び出したプログラムの成功を必ずしも意味しないので注意してください。単に、そのプログラムがタスク プロセッサによって正常に呼び出され、例外が発生することなく後処理が実行されたことを意味するものです。 終了コードの意味は、呼び出したプログラムによって異なります。通常、終了コード 0 は、プログラムが正常に実行されたことを意味し、それ以外の終了コードはプログラムの実行に失敗したことを意味します。 |
| 1 |"予期" されていた箇所のプログラム コードで例外が発生してタスク プロセッサが失敗しました。 この例外は `TaskProcessorException` に変換されます。その際、診断情報のほか、可能であればエラーを解決するための推奨情報が出力されます。 |
| 2 |タスク プロセッサは、"予期しない" 例外で失敗しました。 標準出力に例外が記録されましたが、診断や解決方法に関する情報をタスク プロセッサは追加できませんでした。 |

> [!NOTE]
> 呼び出すプログラムが特定のエラーの種類を示すための終了コードとして 1 と 2 を使用している場合、タスク プロセッサのエラーに終了コード 1 と 2 を使用すると意味が曖昧になります。 このようなタスク プロセッサのエラー コードは、Program.cs ファイルで例外の case 条件を編集し、区別が付くような終了コードに変更してください。
> 
> 

例外によって返されたすべての情報は、stdout.txt ファイルと stderr.txt ファイルに書き込まれます。 詳しくは、Batch のドキュメントでエラー処理に関するページを参照してください。

### <a name="client-considerations"></a>クライアントの考慮事項
**ストレージの資格情報**

タスク プロセッサで Azure Blob Storage を使って (たとえばファイル変換ヘルパー ライブラリを使用して) 出力を永続化する場合、クラウド ストレージ アカウントの資格情報か、"*または*" Shared Access Signature (SAS) を含んだ BLOB コンテナーの URL の "*どちらか*" にアクセスする必要があります。 このテンプレートは、共通の環境変数を通じて資格情報を指定できるようになっています。 ストレージの資格情報は、クライアントから次のようにして渡すことができます。

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

その後は、TaskProcessor クラスから `_configuration.StorageAccount` プロパティを通じてストレージ アカウントにアクセスできます。

SAS を含んだコンテナーの URL を使用する場合は、ジョブの共通の環境設定を通じて渡すこともできます。ただし現時点では、タスク プロセッサ テンプレートにそのための機能は組み込まれていません。

**ストレージのセットアップ**

タスクに必要なコンテナーがあれば、そのタスクをジョブに追加する前に、クライアントまたはジョブ マネージャー タスクで作成することをお勧めします。 SAS を含んだコンテナーの URL を使用する場合は、これが必須となります。そのような URL には、コンテナーを作成するためのアクセス許可が含まれていないためです。 個々のタスクでコンテナーの CloudBlobContainer.CreateIfNotExistsAsync を呼び出さずに済むため、この推奨事項は、ストレージ アカウントの資格情報を渡す場合にも当てはまります。

## <a name="pass-parameters-and-environment-variables"></a>パラメーターと環境変数の受け渡し
### <a name="pass-environment-settings"></a>環境設定の受け渡し
ジョブ マネージャー タスクには、クライアントから環境設定の形式で情報を渡すことができます。 ジョブ マネージャー タスクは、その受け取った情報を使用して、コンピューティング ジョブの一部として実行されるタスク プロセッサ タスクを生成できます。 次に示したのは、環境設定として渡すことができる情報の例です。

* ストレージ アカウント名とアカウント キー
* Batch アカウントの URL
* Batch アカウントのキー

Batch サービスでは、[Microsoft.Azure.Batch.JobManagerTask][net_jobmanagertask] の `EnvironmentSettings` プロパティを使用したシンプルなメカニズムによって、ジョブ マネージャー タスクに環境設定を渡すことができます。

たとえば、Batch アカウントの `BatchClient` インスタンスを取得するには、クライアント コードから Batch アカウントの URL と共有キーの資格情報を環境変数として渡します。 同様に、Batch アカウントにリンクされたストレージ アカウントにアクセスする場合は、そのストレージ アカウント名とストレージ アカウント キーを環境変数として渡します。

### <a name="pass-parameters-to-the-job-manager-template"></a>ジョブ マネージャー テンプレートへのパラメーターの受け渡し
ほとんどの場合、ジョブ単位のパラメーターは、ジョブ マネージャー タスクに渡すことをお勧めします。ジョブの分割プロセスを制御したり、ジョブのタスクを構成したりする目的で利用できます。 そのためには、ジョブ マネージャー タスクのリソース ファイルとして parameters.json という JSON ファイルをアップロードします。 これで必要なパラメーターが、ジョブ マネージャー テンプレートの `JobSplitter._parameters` フィールドを通じて利用できる状態になります。

> [!NOTE]
> 組み込みのパラメーター ハンドラーがサポートしているのは、文字列対文字列のディクショナリのみです。 複雑な JSON 値をパラメーターの値として渡す必要がある場合は、それらを文字列として渡して、ジョブ スプリッターで解析するか、またはジョブ マネージャー フレームワークの `Configuration.GetJobParameters` メソッドを変更する必要があります。
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>タスク プロセッサ テンプレートへのパラメーターの受け渡し
タスク プロセッサ テンプレートを使用して実装した個々のタスクにパラメーターを渡すこともできます。 ジョブ マネージャー テンプレートと同様、タスク プロセッサ テンプレートも、

parameters.json というリソース ファイルを探し、見つかった場合は、それをパラメーター ディクショナリとして読み込みます。 タスク プロセッサ タスクにパラメーターを渡す方法としては、以下の 2 つの選択肢があります。

* ジョブの parameters.json を再利用する。 この方法は、ジョブ スコープのパラメーターのみである場合に利用できます (レンダリングの高さと幅など)。 具体的には、ジョブ スプリッターで CloudTask を作成するときに、ジョブ マネージャー タスクの ResourceFiles (`JobSplitter._jobManagerTask.ResourceFiles`) から CloudTask の ResourceFiles コレクションに parameters.json リソース ファイル オブジェクトへの参照を追加します。
* ジョブ スプリッターの実行過程でタスク固有の parameters.json ドキュメントを生成、アップロードしておき、その BLOB をタスクのリソース ファイル コレクション内から参照する。 タスクごとにパラメーターが異なる場合、この方法が必要になります。 たとえば 3D レンダリングで、フレーム インデックスをタスクにパラメーターとして渡すような状況が該当します。

> [!NOTE]
> 組み込みのパラメーター ハンドラーがサポートしているのは、文字列対文字列のディクショナリのみです。 複雑な JSON 値をパラメーターの値として渡す必要がある場合は、それらを文字列として渡して、タスク プロセッサで解析するか、またはタスク プロセッサ フレームワークの `Configuration.GetTaskParameters` メソッドを変更する必要があります。
> 
> 

## <a name="next-steps"></a>次の手順
### <a name="persist-job-and-task-output-to-azure-storage"></a>ジョブやタスクからの出力を Azure Storage に保存する
Batch ソリューション開発に有効活用できるもう 1 つのツールとして、[Azure Batch ファイル規則][nuget_package]があります。 Batch .NET アプリケーションにこの .NET クラス ライブラリ (現在はプレビュー段階) を使用することで、タスクからの出力を Azure Storage との間で簡単に保存したり取り出したりすることができます。 [Azure Batch のジョブとタスクの出力の保持](batch-task-output.md) 」をご覧ください。


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://go.microsoft.com/fwlink/?linkid=820714
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
