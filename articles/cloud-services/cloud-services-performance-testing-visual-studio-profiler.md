---
title: コンピューティング エミュレーターでのクラウド サービス (クラシック) のローカルなプロファイル | Microsoft Docs
description: Visual Studio プロファイラーを使用して、クラウド サービスのパフォーマンスの問題を調査します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2f924d84967c1a1928a47b59fd3a8c28da091130
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743561"
---
# <a name="testing-the-performance-of-a-cloud-service-classic-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Visual Studio プロファイラーを使用した、Azure Compute Emulator でのクラウド サービス (クラシック) のパフォーマンスのローカルなテスト

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

クラウド サービスのパフォーマンスのテストには、さまざまなツールや手法を使用できます。
Azure にクラウド サービスを発行する場合は、[Azure アプリケーションのプロファイリング][1]に関するページで説明されているように、Visual Studio でプロファイリング データを収集してから、それをローカルで分析することができます。
また、[Azure でのパフォーマンス カウンターの使用][2]に関するページで説明されているように、診断を使用してさまざまなパフォーマンス カウンターを追跡することもできます。
アプリケーションをクラウドにデプロイする前に、コンピューティング エミュレーターでローカルにプロファイルすることもできます。

この記事では、エミュレーターでローカルに実行できるプロファイル手法である CPU サンプリングについて説明します。 CPU サンプリングは、あまり侵入的でないプロファイル手法です。 プロファイラーは、指定されたサンプリング間隔でコール スタックのスナップショットを取得します。 データはある期間にわたって収集され、レポートに示されます。 このプロファイル手法では、コンピューティング処理が集中するアプリケーションで大部分の CPU 処理が行われる箇所が示されます。  これによって、アプリケーションが多くの時間を費やしている "ホット パス" に焦点を合わせる機会が与えられます。

## <a name="1-configure-visual-studio-for-profiling"></a>1:Visual Studio をプロファイル向けに構成する
まず、プロファイル時に有用な Visual Studio の構成オプションがいくつかあります。 プロファイル レポートを理解しやすくするために、アプリケーションのシンボル (.pdb ファイル) およびシステム ライブラリのシンボルが必要です。 使用可能なシンボル サーバーを参照していることを確認してください。 確認するには、Visual Studio の **[ツール]** メニューで、 **[オプション]** 、 **[デバッグ]** 、 **[シンボル]** の順に選択します。 Microsoft シンボル サーバーが **[シンボル ファイル (.pdb) の場所]** の下に表示されていることを確認します。  https://referencesource.microsoft.com/symbols を参照することもできます。追加のシンボル ファイルが含まれる可能性があります。

![Symbol options][4]

必要な場合は、[マイ コードのみ] を設定して、プロファイラーが生成するレポートを簡素化することができます。 [マイ コードのみ] を有効にすると、完全にライブラリおよび .NET Framework の内部への呼び出しがレポートから隠されるように関数コール スタックが簡素化されます。 **[ツール]** メニューの **[オプション]** を選択します。 次に、 **[パフォーマンス ツール]** ノードを展開し、 **[全般]** を選択します。 **[プロファイラー レポートでマイ コードのみを有効にする]** チェック ボックスをオンにします。

![Just My Code options][17]

これらの手順は、既存のプロジェクトまたは新規プロジェクトに適用できます。  以降で説明する手法を試すために新規プロジェクトを作成する場合は、C# の **[Azure クラウド サービス]** プロジェクトを選択し、 **[Web ロール]** および **[ワーカー ロール]** を選択します。

![Azure Cloud Service project roles][5]

例として、時間がかかり明白なパフォーマンス問題を示すコードをプロジェクトに追加してください。 たとえば、worker ロール プロジェクトに次のコードを追加します。

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

worker ロールの RoleEntryPoint から派生したクラスの RunAsync メソッドからこのコードを呼び出します (同期実行されるメソッドに関する警告は無視してください)。

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

ソリューション構成を **[リリース]** に設定して、デバッグ (Ctrl + F5 キー) を行わずに、クラウド サービスをローカルでビルドして実行します。 これによって、アプリケーションをローカルに実行するためにすべてのファイルとフォルダーが作成され、すべてのエミュレーターが起動されます。 コンピューティング エミュレーター UI をタスク バーから起動して、worker ロールが実行していることを確認します。

## <a name="2-attach-to-a-process"></a>2:プロセスにアタッチする
アプリケーションを Visual Studio 2010 IDE から起動してプロファイルするのではなく、実行中のプロセスにプロファイラーをアタッチする必要があります。 

プロファイラーをプロセスにアタッチするには、 **[分析]** メニューで **[プロファイラー]** 、 **[アタッチ/デタッチ]** の順に選択します。

![Attach profile option][6]

worker ロールの場合は、WaWorkerHost.exe プロセスを見つけます。

![WaWorkerHost process][7]

プロジェクト フォルダーがネットワーク ドライブ上にある場合は、プロファイル レポートの保存場所として別の場所を提供するように求められます。

 WaIISHost.exe にアタッチすることで Web ロールにアタッチすることもできます。
アプリケーション内に複数の worker ロールがある場合は、processID を使用してそれらを区別する必要があります。 Process オブジェクトにアクセスすることで、プログラムで processID を照会できます。 たとえば、ロール内の RoleEntryPoint から派生したクラスの Run メソッドに次のコードを追加すると、コンピューティング エミュレーター UI でログを調べて、接続しているプロセスを知ることができます。

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

ログを表示するには、コンピューティング エミュレーター UI を起動します。

![Start the Compute Emulator UI][8]

コンピューティング エミュレーター UI で、コンソール ウィンドウのタイトル バーをクリックして worker ロール ログのコンソール ウィンドウを開きます。 ログに "Process ID" が表示されています。

![View process ID][9]

アタッチしたプロセスについて、アプリケーションの UI で手順に従って (必要な場合) シナリオを再現します。

プロファイルを停止する場合は、 **[プロファイルの停止]** を選択します。

![Stop Profiling option][10]

## <a name="3-view-performance-reports"></a>3:パフォーマンス レポートを表示する
アプリケーションのパフォーマンス レポートが表示されます。

この時点で、プロファイラーは実行を停止し、.vsp ファイルにデータを保存し、そのデータの分析を示すレポートを表示します。

![Profiler report][11]

[ホット パス] に "String.wstrcpy" が表示されている場合は、[マイ コードのみ] をクリックして、ユーザー コードだけを表示するようにビューを変更します。  "String.Concat" が表示されている場合は、[すべてのコードの表示] を押してみてください。

Concatenate メソッドと String.Concat が実行時間の大部分を占めていることがわかります。

![Analysis of report][12]

この記事で文字列連結コードを追加した場合は、それに関する [タスク リスト] に警告が表示されます。 作成および処理される文字列の数によっては、過剰な量のガベージ コレクションがあるという警告が表示されることもあります。

![Performance warnings][14]

## <a name="4-make-changes-and-compare-performance"></a>4:変更を加えてパフォーマンスを比較する
コードを変更する前後でパフォーマンスを比較することもできます。  実行中のプロセスを停止します。コードを編集して、文字列連結操作を StringBuilder の使用に置き換えます。

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

パフォーマンス測定用にもう一度実行し、パフォーマンスを比較します。 パフォーマンス エクスプローラーで、2 回の実行が同じセッションにある場合は、両方のレポートを選択し、ショートカット メニューを開いて **[パフォーマンス レポートの比較]** を選択します。 別のパフォーマンス セッション内の実行と比較する場合は、 **[分析]** メニューを開いて **[パフォーマンス レポートの比較]** を選択します。 表示されるダイアログ ボックスで、両方のファイルを指定します。

![Compare performance reports option][15]

レポートに 2 つの実行の相違点が示されます。

![Comparison report][16]

お疲れさまでした。 プロファイラーの使用を開始しました。

## <a name="troubleshooting"></a>トラブルシューティング
* リリース ビルドをプロファイルしていることを確認し、デバッグを行わずに起動します。
* [プロファイラー] メニューで [アタッチ/デタッチ] オプションが有効になっていない場合は、パフォーマンス ウィザードを実行します。
* コンピューティング エミュレーター UI を使用して、アプリケーションの状態を表示します。 
* エミュレーターでのアプリケーションの起動や、プロファイラーのアタッチに問題がある場合は、コンピューティング エミュレーターをシャットダウンし、再起動します。 これで問題が解決しない場合は、コンピューターを再起動してみてください。 コンピューティング エミュレーターを使用して、実行中のデプロイメントを中断および削除すると、この問題が発生することがあります。
* コマンド ラインからいずれかのプロファイル コマンドを使用した場合は (特にグローバル設定)、VSPerfClrEnv /globaloff が呼び出され、VsPerfMon.exe がシャットダウンされたことを確認します。
* サンプリング時にメッセージ "PRF0025:データは収集されませんでした" が表示された場合は、アタッチ先のプロセスに CPU アクティビティがあることをチェックします。 コンピューティング作業を行っていないアプリケーションは、サンプリング データを生成しないことがあります。  また、サンプリングが行われる前にプロセスが終了した可能性もあります。 プロファイル中のロールの Run メソッドが終了していないことをチェックします。

## <a name="next-steps"></a>次の手順
Visual Studio プロファイラーでは、エミュレーター内の Azure バイナリのインストルメント化はサポートされていませんが、メモリの割り当てをテストする場合は、プロファイル時にこのオプションを選択できます。 また、スレッドがロックの競合のために時間を浪費しているかどうかの判断に役立つコンカレンシー プロファイルを選択することも、アプリケーションの層間 (最も多いのはデータ層と worker ロールの間) で相互作用するときのパフォーマンス問題の追跡に役立つ階層の相互作用のプロファイルを選択することもできます。  アプリケーションが生成するデータベース クエリを表示し、プロファイル データを使用してデータベースの使用を強化することができます。 階層の相互作用プロファイルの詳細については、ブログ記事「[Walkthrough:Using the Tier Interaction Profiler in Visual Studio Team System 2010 (チュートリアル: Visual Studio Team System 2010 での階層の相互作用のプロファイラーの使用)][3]」を参照してください。

[1]: ../azure-monitor/app/profiler.md
[2]: /previous-versions/azure/hh411542(v=azure.100)
[3]: /archive/blogs/habibh/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
