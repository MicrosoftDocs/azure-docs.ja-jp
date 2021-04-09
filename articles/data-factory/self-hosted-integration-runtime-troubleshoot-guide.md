---
title: Azure Data Factory でセルフホステッド統合ランタイムのトラブルシューティングを行う
description: Azure Data Factory でセルフホステッド統合ランタイムの問題をトラブルシューティングする方法について説明します。
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: lle
ms.openlocfilehash: 2cb0e0870b32270340e37d54dc54a43b22ee014a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376464"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory でのセルフホステッド統合ランタイム (IR) の一般的なトラブルシューティング方法について説明します。

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Azure Data Factory からセルフホステッド IR ログを収集する

セルフホステッド IR または共有 IR で実行されているアクティビティが失敗した場合、Azure Data Factory ではエラー ログの表示とアップロードがサポートされます。 エラー レポート ID を取得するには、こちらの手順に従い、レポート ID を入力して関連する既知の問題を検索します。

1. Data Factory で、 **[パイプライン実行]** を選択します。

1. 次のスクリーンショットに示すように、 **[アクティビティの実行]** の下の **[エラー]** 列で、強調表示されたボタンを選択してアクティビティ ログを表示します。

    ![[すべてのパイプライン実行] ペインにある [アクティビティの実行] セクションのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

    失敗したアクティビティの実行に関するアクティビティ ログが表示されます。

    ![失敗したアクティビティのアクティビティ ログのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png) 
    
1. さらに支援が必要な場合は、 **[ログの送信]** を選択してください。
 
   **[Share the self-hosted integration runtime (IR) logs with Microsoft]\(セルフホステッド統合ランタイム (IR) ログを Microsoft と共有する\)** ウィンドウが開きます。

    ![[Share the self-hosted integration runtime (IR) logs with Microsoft]\(セルフホステッド統合ランタイム (IR) ログを Microsoft と共有する\) ウィンドウのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 送信するログを選択します。 
    * "*セルフホステッド IR*" では、失敗したアクティビティに関連するログ、またはセルフホステッド IR ノード上のすべてのログをアップロードできます。 
    * "*共有 IR*" では、失敗したアクティビティに関連するログのみをアップロードできます。

1. ログがアップロードされたら、問題を解決するためにさらに支援が必要な場合に後で使用できるように、レポート ID を記録しておきます。

    ![IR ログの [アップロードの進行状況] ウィンドウに表示されているレポート ID のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> ログの表示とアップロードの要求は、すべてのオンライン セルフホステッド IR インスタンスで実行されます。 いずれかのログが欠落している場合は、すべてのセルフホステッド IR インスタンスがオンラインになっていることを確認してください。 


## <a name="self-hosted-ir-general-failure-or-error"></a>セルフホステッド IR の一般的な障害またはエラー

### <a name="out-of-memory-issue"></a>メモリ不足の問題

#### <a name="symptoms"></a>現象

リンクされた IR またはセルフホステッド IR を使用してルックアップ アクティビティを実行しようとすると、OutOfMemoryException (OOM) エラーが発生します。

#### <a name="cause"></a>原因

IR マシンで瞬間的にメモリが大量に使用された場合、新しいアクティビティによって OOM エラーがスローされることがあります。 この問題は、大量の同時実行アクティビティが原因で発生する可能性があります。このエラーは設計によるものです。

#### <a name="resolution"></a>解決方法

IR ノードでリソース使用量とアクティビティの同時実行を確認してください。 1 つの IR ノードでの同時実行が過剰にならないように、アクティビティ実行の内部およびトリガーの時間を調整します。

### <a name="concurrent-jobs-limit-issue"></a>コンカレント ジョブの制限の問題

#### <a name="symptoms"></a>現象

Azure Data Factory インターフェイスからコンカレント ジョブの制限を引き上げようとすると、 *[更新中]* 状態でプロセスがハングします。

サンプル シナリオ: コンカレント ジョブの最大値は現在 24 に設定されていますが、ジョブの実行速度を上げるために、この数を増やしたいと考えています。 入力できる最小値は 3 で、最大値は 32 です。 値を 24 から 32 に増やしてから、 **[更新]** ボタンを選択します。 次のスクリーンショットに示すように、プロセスは *[更新中]* の状態でスタックします。 ページを更新しても、値は 24 と表示されたままです。 予期していた 32 には更新されていません。

![統合ランタイムの [ノード] ペインのスクリーンショット。[更新中] 状態でスタックしているプロセスが表示されています。](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>原因

コンカレント ジョブ数の制限は、コンピューターの論理コアとメモリによって決まります。 値を 24 などの値に下方に調整して、結果を表示してみてください。

> [!TIP] 
> -    論理コア数の詳細を確認し、使用するマシンの論理コア数を判別するには、[Windows 10 で CPU のコア数を調べる 4 つの方法](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)に関するページを参照してください。
> -    math.log を計算する方法については、[対数計算機](https://www.rapidtables.com/calc/math/Log_Calculator.html)のページにアクセスしてください。


### <a name="self-hosted-ir-high-availability-ha-ssl-certificate-issue"></a>セルフホステッド IR 高可用性 (HA) の SSL 証明書の問題

#### <a name="symptoms"></a>現象

セルフホステッド IR のワーク ノードで次のエラーが報告されました。

"Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. (プライマリ ノード net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/ から共有状態をプルできませんでした。) Activity ID:XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. (アクティビティ ID: XXXXX。X.509 証明書 CN=abc.cloud.corp.Microsoft.com、OU=test、O=Microsoft チェーンの作成に失敗しました。) 使用された証明書には、検証できない信頼チェーンが含まれています。 証明書を交換するか、certificateValidationMode を変更してください。 The revocation function was unable to check revocation because the revocation server was offline. (失効サーバーがオフラインであったため、失効関数が失効を確認できませんでした。)"

#### <a name="cause"></a>原因

SSL/TLS ハンドシェイクに関連したケースを処理しているときに、証明書チェーンの検証に関連した問題が発生することがあります。 

#### <a name="resolution"></a>解決方法

- X.509 証明書チェーンの作成エラーをトラブルシューティングするための簡単で直感的な方法を次に示します。
 
    1. 検証が必要な証明書をエクスポートします。 そのためには、次の手順を実行します。
    
       a. Windows で **[スタート]** を選択し、「**証明書**」と入力し始めて、 **[コンピューター証明書の管理]** を選択します。
       
       b. エクスプローラーの左側のペインで、確認する証明書を検索し、それを右クリックしてから、 **[すべてのタスク]**  >  **[エクスポート]** を選択します。
    
        ![[コンピューター証明書の管理] ペインでの証明書に対する [すべてのタスク] > [エクスポート] コントロールのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. エクスポートされた証明書をクライアント マシンにコピーします。 
    3. クライアント側のコマンド プロンプト ウィンドウで、次のコマンドを実行します。 必ず、 *\<certificate path>* と *\<output txt file path>* を実際のパスに置き換えてください。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        例:

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. 出力 TXT ファイルにエラーがないかどうかを確認します。 エラーの概要は、この TXT ファイルの最後にあります。

        例: 

        ![TXT ファイルの最後にあるエラーの概要のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        次のスクリーンショットに示すように、ログ ファイルの最後にエラーがない場合は、証明書チェーンがクライアント マシンで正常に作成されたと考えることができます。
        
        ![エラーが表示されていないログ ファイルのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- AIA (機関情報アクセス)、CDP (CRL 配布ポイント)、または OCSP (オンライン証明書状態プロトコル) のファイル名拡張子が証明書ファイルに構成されている場合は、より直観的な方法で確認できます。
 
    1. 次のスクリーンショットに示されているように、証明書の詳細を確認してこの情報を取得します。
    
        ![証明書の詳細のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    
    1. 次のコマンドを実行します。 *\<certificate path>* は、必ず証明書の実際のパスに置き換えてください。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    
        URL 取得ツールが開きます。 
        
    1. AIA、CDP、および OCSP のファイル名拡張子を持つ証明書を確認するには、 **[取得]** を選択します。

        ![URL 取得ツールと [取得] ボタンのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        AIA からの証明書の状態が "*検証済み*" で、なおかつ CDP または OCSP からの証明書の状態が "*検証済み*" であれば、証明書チェーンは正常に作成されています。

        AIA または CDP を取得しようとして失敗した場合は、ネットワーク チームと協力して、クライアント マシンをターゲット URL に接続できる状態にしてください。 HTTP パスまたはライトウェイト ディレクトリ アクセス プロトコル (LDAP) パスのいずれかを確認できれば十分です。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>セルフホステッド IR がファイルまたはアセンブリを読み込めない

#### <a name="symptoms"></a>現象

次のエラー メッセージが表示されます。

"Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. (ファイルまたはアセンブリ 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX'、またはその依存関係の 1 つを読み込めませんでした。) 指定されたファイルが見つかりません。 Activity ID: (アクティビティ ID:) 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"
 
より具体的なエラー メッセージを次に示します。 

"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. (ファイルまたはアセンブリ 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX'、またはその依存関係の 1 つを読み込めませんでした。) 指定されたファイルが見つかりません。 Activity ID: (アクティビティ ID:) 92693b45-b4bf-4fc8-89da-2d3dc56f27c3"

#### <a name="cause"></a>原因

プロセス モニターで、次の結果を表示できます。

[![プロセス モニター内のパス一覧のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> プロセス モニターで、次のスクリーンショットに示すようにフィルターを設定できます。
>
> 前のエラー メッセージは、DLL System.ValueTuple が、関連する *グローバル アセンブリ キャッシュ* (GAC) フォルダー、*C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* フォルダー、または *C:\Program Files\Microsoft Integration Runtime\4.0\Shared* フォルダー内にないことを示しています。
>
> 基本的に、このプロセスでは、まず *GAC* フォルダーから、次に "*共有*" フォルダーから、そして最後に "*ゲートウェイ*" フォルダーから DLL が読み込まれます。 そのため、有用なパスであれば、どのパスからでも DLL を読み込むことができます。

<br>

![[プロセス モニター フィルター] ページのスクリーンショット。DLL のフィルターが一覧表示されています。](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>解決方法

*System.ValueTuple.dll* ファイルは *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* フォルダーにあります。 この問題を解決するには、*System.ValueTuple.dll* ファイルを *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* フォルダーにコピーします。

その他の欠落しているファイルやアセンブリの問題も同じ方法を使用して解決できます。

#### <a name="more-information-about-this-issue"></a>この問題に関する追加情報

*System.ValueTuple.dll* が *%windir%\Microsoft.NET\assembly* や *%windir%\assembly* の下に表示される理由は、これが .NET の動作だからです。 

次のエラーでは、*System.ValueTuple* アセンブリが欠落していることを明確に確認できます。 この問題は、アプリケーションが *System.ValueTuple.dll* アセンブリを確認しようとしたときに発生します。
 
"\<LogProperties>\<ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. (\<LogProperties>\<ErrorInfo>[{"Code":0,"Message":"'Npgsql.PoolManager' のタイプ初期化子が例外をスローしました。","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"ファイルまたはアセンブリ 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX'、またはその依存関係の 1 つを読み込めませんでした。) The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]\</ErrorInfo>\</LogProperties> (指定されたファイルが見つかりません。","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]\</ErrorInfo>\</LogProperties>)"
 
GAC の詳細については、「[グローバル アセンブリ キャッシュ](/dotnet/framework/app-domains/gac)」を参照してください。


### <a name="self-hosted-integration-runtime-authentication-key-is-missing"></a>セルフホステッド統合ランタイムの認証キーがない

#### <a name="symptoms"></a>現象

セルフホステッド統合ランタイムが認証キーなしで突然オフラインになり、イベント ログに次のエラー メッセージが表示されます。 

"Authentication Key is not assigned yet (認証キーがまだ割り当てられていません)"

![認証キーがまだ割り当てられていないことを示す統合ランタイムのイベント ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>原因

- Azure portal のセルフホステッド IR ノードまたは論理セルフホステッド IR が削除された。
- クリーン アンインストールが実行された。

#### <a name="resolution"></a>解決方法

前述のどちらの原因も該当しない場合は、 *%programdata%\Microsoft\Data Transfer\DataManagementGateway* フォルダーに移動して、*Configurations* ファイルが削除されているかどうかを確認してください。 削除されていた場合は、[Windows ファイル サーバーからファイルを削除したユーザーの検出](https://www.netwrix.com/how_to_detect_who_deleted_file.html)に関する Netwrix の記事の指示に従ってください。

![Configurations ファイルを確認するためのイベント ログの詳細ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cant-use-self-hosted-ir-to-bridge-two-on-premises-datastores"></a>セルフホステッド IR を使用して 2 つのオンプレミス データ ストアをブリッジできない

#### <a name="symptoms"></a>現象

ソースと宛先の両方のデータストアに対してセルフホステッド IR を作成した後、その 2 つの IR を接続してコピー アクティビティを完了したいと考えています。 データストアが別の仮想ネットワークに構成されている場合、またはデータストアによってゲートウェイのメカニズムが理解されない場合は、次のいずれかのエラーが発生します。 

* "The driver of source cannot be found in destination IR (ソースのドライバーが宛先の IR で見つかりません。)"
* "The source cannot be accessed by the destination IR (宛先の IR がソースにアクセスできません)"
 
#### <a name="cause"></a>原因

セルフホステッド IR は、それぞれのデータストアにインストールする必要のあるクライアント エージェントとしてではなく、コピー アクティビティの中央ノードとして設計されています。
 
この場合は、同じ IR を使用してデータストアごとにリンクされたサービスを作成する必要があり、IR はネットワーク経由で両方のデータストアにアクセスできる必要があります。 IR がインストールされているのがソース データストアか宛先データストアか、または第 3 のマシンかは問題ではありません。 2 つのリンクされたサービスが異なる IR を使用して作成されているが、同じコピー アクティビティで使用されている場合は、宛先 IR が使用されるため、両方のデータストアのドライバーを宛先 IR マシンにインストールする必要があります。

#### <a name="resolution"></a>解決方法

ソースと宛先両方のデータストアのドライバーを宛先 IR にインストールし、そこからソース データストアにアクセスできることを確認します。
 
2 つのデータストア間のネットワークをトラフィックが通過できない場合 (それらが 2 つの仮想ネットワークに構成されている場合など)、IR がインストールされていても、1 回のアクティビティでコピーが完了しない可能性があります。 1 回のアクティビティでコピーを完了できない場合は、それぞれが 1 つの VNET 内にある 2 つの IR を使用して 2 つのコピー アクティビティを作成できます。 
* 1 つの IR をデータストア 1 から Azure Blob Storage にコピーします。
* 別の IR を Azure Blob Storage からデータストア 2 にコピーします。 

このソリューションでは、IR を使用して 2 つの切断されたデータストアを接続するブリッジを作成するための要件をシミュレートできます。


### <a name="credential-sync-issue-causes-credential-loss-from-ha"></a>資格情報の同期の問題によって HA から資格情報が失われる

#### <a name="symptoms"></a>現象

データ ソース資格情報 "XXXXXXXXXX" が、ペイロードが含まれている現在の統合ランタイム ノードから削除された場合、次のエラー メッセージが表示されます。

"When you delete the link service on Azure portal, or the task has the wrong payload, please create new link service with your credential again. (Azure portal のリンク　サービスを削除した場合、またはタスクのペイロードが正しくない場合は、資格情報を使用して新しいリンク サービスをもう一度作成してください。)"

#### <a name="cause"></a>原因

セルフホステッド IR は 2 つのノードを使用した HA モードで構築されていますが、それらのノードで資格情報が同期状態になっていません。 これは、ディスパッチャー ノードに格納されている資格情報が他のワーカー ノードと同期されていないことを意味します。 ディスパッチャー ノードからワーカー ノードへのフェールオーバーが発生したときに、前のディスパッチャー ノードにしか資格情報が存在しない場合、資格情報にアクセスを試みた時点でそのタスクは失敗し、上記のエラーが発生します。

#### <a name="resolution"></a>解決方法

この問題を回避する唯一の方法は、2 つのノードで確実に資格情報を同期状態にすることです。 同期されていない場合は、新しいディスパッチャーの資格情報を再入力する必要があります。


### <a name="cant-choose-the-certificate-because-the-private-key-is-missing"></a>秘密キーがないため、証明書を選択できない

#### <a name="symptoms"></a>現象

* 証明書ストアに PFX ファイルをインポートしました。
* IR Configuration Manager UI で証明書を選択したときに、次のエラー メッセージが表示されました。

   "Failed to change intranet communication encryption mode. (イントラネット通信の暗号化モードを変更できませんでした。) It is likely that certificate '\<*certificate name*>' may not have a private key that is capable of key exchange or the process may not have access rights for the private key. (証明書 "\<*certificate name*>" に、キー交換が可能な秘密キーがないか、プロセスに秘密キーへのアクセス権がない可能性があります。) Please see inner exception for detail. (詳細については、内部例外を参照してください。)"

    ![Integration Runtime Configuration Manager 設定ペインのスクリーンショット。"秘密キーが見つかりません" というエラー メッセージが表示されています。](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>原因

- ユーザー アカウントの特権レベルが低いため、秘密キーにアクセスできません。
- 証明書はキー交換としてではなく、署名として生成されました。

#### <a name="resolution"></a>解決方法

* UI を操作するには、秘密キーにアクセスするための適切な特権を持つアカウントを使用します。  
* 次のコマンドを実行して証明書をインポートします。
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```

## <a name="self-hosted-ir-setup"></a>セルフホステッド IR の設定

### <a name="integration-runtime-registration-error"></a>統合ランタイムの登録エラー 

#### <a name="symptoms"></a>現象

次のいずれかの理由で、セルフホステッド IR を別のアカウントで実行することが必要になる場合があります。
- サービス アカウントが会社のポリシーで許可されていない。
- なんらかの認証が必要である。

サービス ペインでサービス アカウントを変更した後に、統合ランタイムが動作を停止し、次のエラー メッセージが表示される場合があります。

"The Integration Runtime (Self-hosted) node has encountered an error during registration. (登録中に Integration Runtime (セルフホステッド) ノードでエラーが発生しました。) Cannot connect to the Integration Runtime (Self-hosted) Host Service. (Integration Runtime (セルフホステッド) ホスト サービスに接続できません。)"

![IR 登録エラーを示す Integration Runtime Configuration Manager ウィンドウのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>原因

多くのリソースは、サービス アカウントにのみ付与されます。 サービス アカウントを別のアカウントに変更しても、依存するすべてのリソースのアクセス許可は変更されません。

#### <a name="resolution"></a>解決方法

統合ランタイムのイベント ログにアクセスしてエラーを確認してください。

![ランタイム エラーが発生したことを示す IR イベント ログのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

* イベント ログのエラーが "UnauthorizedAccessException" の場合は、次の手順を実行します。

    1. Windows サービス パネルで *DIAHostService* ログオン サービス アカウントを確認します。

        ![ログオン サービス アカウントのプロパティ ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. ログオン サービス アカウントに、 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* フォルダーへの読み取りおよび書き込みアクセス許可があるかどうかを確認します。

        - サービスのログオン アカウントが変更されていなければ、既定で読み取りおよび書き込みアクセス許可が割り当てられているはずです。

            ![サービスのアクセス許可ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

        - サービスのログオン アカウントを変更した場合は、次の手順を実行して問題を軽減してください。
 
            a. 現在のセルフホステッド IR のクリーン アンインストールを実行します。   
            b. セルフホステッド IR ビットをインストールします。  
            c. 次の手順を実行して、サービス アカウントを変更します。  

             i. セルフホステッド IR のインストール フォルダーに移動し、*Microsoft Integration Runtime\4.0\Shared* フォルダーに切り替えます。  
             ii. 管理者特権を使用してコマンド プロンプト ウィンドウを開きます。 *\<user>* と *\<password>* を実際のユーザー名とパスワードに置き換えてから、次のコマンドを実行します。   
                `dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"`  
             iii. LocalSystem アカウントに変更したい場合は、そのアカウントの正しい形式を使用してください: `dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""`  
                次の形式は使用 "*しない*" でください: `dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""`     
             iv. LocalSystem には管理者よりも高い特権があるため、必要な場合、[サービス] で直接それを変更することもできます。  
             v. IR サービスのログオン アカウントには、ローカルまたはドメインのユーザーを使用できます。            

            d. 統合ランタイムを登録します。

* エラーが、"Service 'Integration Runtime Service' (DIAHostService) failed to start. Verify that you have sufficient privileges to start system services (サービス 'Integration Runtime Service' (DIAHostService) を開始できませんでした。システム サービスを開始するのに十分な特権があることを確認してください。)" の場合、次の手順を実行します。

    1. Windows サービス パネルで *DIAHostService* ログオン サービス アカウントを確認します。
    
        ![サービス アカウントの [ログオン] ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

    1. そのログオン サービス アカウントに、Windows サービスを開始するための **[サービスとしてログオン]** アクセス許可があるかどうかを確認します。

        ![[サービスとしてログオン] プロパティ ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>説明

前述の 2 つの解決パターンのいずれも当てはまらない場合は、次の Windows イベント ログを収集してみてください。 
- [アプリケーションとサービス ログ] > [Integration Runtime]
- [Windows ログ] > [アプリケーション]

### <a name="cant-find-the-register-button-to-register-a-self-hosted-ir"></a>セルフホステッド IR を登録するための [登録] ボタンが見つからない    

#### <a name="symptoms"></a>現象

セルフホステッド IR を登録するときに、Configuration Manager ペインに **[登録]** ボタンが表示されません。

![Configuration Manager ペインのスクリーンショット。統合ランタイム ノードが登録されていないことを示すメッセージが表示されています。](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>原因

Integration Runtime 3.0 のリリース時点で、よりクリーンでセキュアな環境を実現するために、既存の統合ランタイム ノードの **[登録]** ボタンが削除されました。 オンラインかどうかにかかわらず、ノードが統合ランタイムに登録されている場合、それを別の統合ランタイムに再登録するには、前のノードをアンインストールしてから、ノードをインストールして登録する必要があります。

#### <a name="resolution"></a>解決方法

1. コントロール パネルで、既存の統合ランタイムをアンインストールします。

    > [!IMPORTANT] 
    > 次のプロセスで、 **[はい]** を選択します。 アンインストール プロセス中はデータを保持しないでください。

    ![統合ランタイムからすべてのユーザー データを削除するための [はい] ボタンのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. 統合ランタイム インストーラーの MSI ファイルがない場合は、[ダウンロード センター](https://www.microsoft.com/en-sg/download/details.aspx?id=39717)にアクセスして、最新の統合ランタイムをダウンロードしてください。
1. MSI ファイルをインストールし、統合ランタイムを登録します。


### <a name="unable-to-register-the-self-hosted-ir-because-of-localhost"></a>localhost が原因でセルフホステッド IR を登録できない    

#### <a name="symptoms"></a>現象

get_LoopbackIpOrName を使用しているときは、セルフホステッド IR を新しいマシンに登録できません。

**デバッグ:** 実行時エラーが発生しました。
'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' のタイプ初期化子が例外をスローしました。
データベースの参照中に、修復できないエラーが発生しました。
 
**例外の詳細:** System.TypeInitializationException: 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' のタイプ初期化子が例外をスローしました。 ---> System.Net.Sockets.SocketException:データベースの参照中に、System.Net.Dns.GetAddrInfo(String name) で修復できないエラーが発生しました。

#### <a name="cause"></a>原因

この問題は、通常、localhost の解決中に発生します。

#### <a name="resolution"></a>解決方法

localhost の IP アドレス127.0.0.1 を使用してファイルをホストし、問題を解決します。

### <a name="self-hosted-setup-failed"></a>セルフホステッドの設定に失敗した    

#### <a name="symptoms"></a>現象

既存の IR のアンインストール、新しい IR のインストール、または既存の IR から新しい IR へのアップグレードができません。

#### <a name="cause"></a>原因

統合ランタイムのインストールは、Windows インストーラー サービスに依存します。 インストールの問題は、次の理由で発生する可能性があります。
- 使用可能なディスク領域が不足している。
- 必要なアクセス許可がない。
- Windows NT サービスがロックされている。
- CPU 使用率が高すぎる。
- ネットワークの速度が遅い場所で MSI ファイルがホストされている。
- 一部のシステム ファイルまたはレジストリに意図しない変更があった。

### <a name="the-ir-service-account-failed-to-fetch-certificate-access"></a>IR サービス アカウントで証明書のアクセス権を取得できなかった。

#### <a name="symptoms"></a>現象

Microsoft Integration Runtime Configuration Manager を使用してセルフホステッド IR をインストールすると、信頼できる証明機関 (CA) の証明書が生成されます。 この証明書を適用して 2 つのノード間の通信を暗号化することができず、次のエラー メッセージが表示されています。 

"Failed to change Intranet communication encryption mode: (イントラネット通信の暗号化モードを変更できませんでした。)Failed to grant Integration Runtime service account the access of to the certificate '\<*certificate name*>'. (Integration Runtime サービス アカウントに、証明書 "\<*certificate name*>" へのアクセス権を付与できませんでした。) Error code 103 (エラー コード 103)"

!["...Failed to grant Integration Runtime service account certificate access (Integration Runtime サービス アカウントに、証明書へのアクセス権を付与できませんでした)" というエラー メッセージが表示されているスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-account-certificate-error.png)

#### <a name="cause"></a>原因

この証明書にはキー格納プロバイダー (KSP) が使用されていますが、これはまだサポートされていません。 現在のところ、セルフホステッド IR では暗号化サービス プロバイダー (CSP) のストレージのみがサポートされています。

#### <a name="resolution"></a>解決方法

この場合、CSP 証明書を使用することをお勧めします。

**解決策 1** 

証明書をインポートするには、次のコマンドを実行します。

`Certutil.exe -CSP "CSP or KSP" -ImportPFX FILENAME.pfx`

![証明書をインポートするための certutil コマンドのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/use-certutil.png)

**解決策 2** 

証明書を変換するには、次のコマンドを実行します。

`openssl pkcs12 -in .\xxxx.pfx -out .\xxxx_new.pem -password pass: <EnterPassword>`
`openssl pkcs12 -export -in .\xxxx_new.pem -out xxxx_new.pfx`

変換前と変換後:

![証明書の変換前の結果のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/before-certificate-change.png)

![証明書の変換後の結果のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/after-certificate-change.png)

### <a name="self-hosted-integration-runtime-version-5x"></a>セルフホステッド統合ランタイム バージョン 5.x
Azure Data Factory セルフホステッド統合ランタイム バージョン 5.x にアップグレードするには、 **.NET Framework ランタイム 4.7.2** 以降が必要です。 ダウンロード ページには、最新の 4.x バージョンと 2 つの最新 5.x バージョン用のダウンロード リンクがあります。 

Azure Data Factory v2 のお客様の場合:
- 自動更新がオンになっていて、.NET Framework ランタイムを 4.7.2 以降に既にアップグレードしている場合、セルフホステッド統合ランタイムは最新の 5.x バージョンに自動的にアップグレードされます。
- 自動更新がオンになっていて、.NET Framework ランタイムを 4.7.2 以降にまだアップグレードしていない場合、セルフホステッド統合ランタイムは最新の 5.x バージョンに自動的にアップグレードされません。 セルフホステッド統合ランタイムは、現在の 4.x バージョンのままになります。 .NET Framework ランタイムのアップグレードに関する警告が、ポータルとセルフホストテッド統合ランタイムのクライアントに表示されます。
- 自動更新がオフになっていて、.NET Framework ランタイムを 4.7.2 以降に既にアップグレードしている場合は、最新の 5.x を手動でダウンロードして、お使いのマシンにインストールできます。
- 自動更新がオフになっていて、.NET Framework ランタイムを 4.7.2 以降にアップグレードしていない場合: セルフホステッド統合ランタイム 5.x を手動でインストールしてキーを登録しようとすると、最初に .NET Framework ランタイムのバージョンをアップグレードするように求められます。


Azure Data Factory v1 のお客様の場合:
- セルフホステッド統合ランタイム 5.x では、Azure Data Factory v1 はサポートされていません。
- セルフホステッド統合ランタイムは、4.x の最新バージョンに自動的にアップグレードされます。 4\.x の最新バージョンは期限切れになりません。 
- セルフホステッド統合ランタイム 5.x を手動でインストールしてキーを登録しようとすると、セルフホステッド統合ランタイム 5.x では Azure Data Factory v1 がサポートされていないことが通知されます。


## <a name="self-hosted-ir-connectivity-issues"></a>セルフホステッド IR の接続の問題

### <a name="self-hosted-integration-runtime-cant-connect-to-the-cloud-service"></a>セルフホステッド統合ランタイムがクラウド サービスに接続できない

#### <a name="symptoms"></a>現象

セルフホステッド統合ランタイムを登録しようとすると、Configuration Manager によって次のエラー メッセージが表示されます。

"The Integration Runtime (Self-hosted) node has encountered an error during registration. (Integration Runtime (セルフホステッド) ノードで登録中にエラーが発生しました。)"

!["The Integration Runtime (Self-hosted) node has encountered an error during registration. (Integration Runtime (セルフホステッド) ノードで登録中にエラーが発生しました。)" メッセージのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

セルフホステッド IR が Azure Data Factory サービス バックエンドに接続できません。 この問題は、通常、ファイアウォールのネットワーク設定が原因で発生します。

#### <a name="resolution"></a>解決方法

1. 統合ランタイム サービスが実行されているかどうかを確認します。 そうであれば、手順 2 に進みます。
    
   ![セルフホステッド IR サービスが実行中であることを示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. セルフホステッド IR にプロキシが構成されていない (既定の設定) 場合は、セルフホステッド統合ランタイムがインストールされているマシン上で次の PowerShell コマンドを実行します。

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > サービスの URL は、データ ファクトリ インスタンスの場所に応じて異なる可能性があります。 サービスの URL を確認するには、 **[ADF UI]**  >  **[接続]**  >  **[統合ランタイム]**  >  **[Edit Self-hosted IR]\(セルフホステッド IR の編集\)**  >  **[ノード]**  >  **[View Service URLs]\(サービスの URL の表示\)** を選択します。
            
    想定される応答を次に示します。
            
    ![PowerShell コマンド応答のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 想定していた応答が得られない場合は、適宜、次のいずれかの方法を使用します。
            
    * "リモート名を解決できませんでした" というメッセージを受け取る場合は、ドメイン ネーム システム (DNS) の問題が発生しています。 この問題を解決するには、ネットワーク チームに問い合わせてください。
    * "ssl/tls cert is not trusted"(ssl/tls 証明書は信頼されていません) というメッセージを受け取る場合は、[証明書をチェック](https://wu2.frontend.clouddatahub.net/)して、それがマシン上で信頼されているかどうかを確認し、次に証明書マネージャーを使用して公開証明書をインストールします。 この操作により、問題が軽減されます。
    * **[Windows]**  >  **[イベント ビューアー (ログ)]**  >  **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** に移動し、DNS、ファイアウォール規則、または会社のネットワーク設定が原因でエラーが発生していないかを確認します。 そのようなエラーが見つかった場合は、接続を強制的に閉じてください。 どの会社にも独自のカスタマイズされたネットワーク設定があるため、これらの問題をトラブルシューティングするには、自社のネットワーク チームにお問い合わせください。

1. セルフホステッド統合ランタイムで "プロキシ" が構成されている場合は、プロキシ サーバーからサービス エンドポイントにアクセスできることを確認します。 サンプル コマンドについては、「[PowerShell, web requests, and proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)」(PowerShell、Web 要求、プロキシ) を参照してください。    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

想定される応答を次に示します。
            
![想定される PowerShell コマンド応答のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> プロキシに関する考慮事項：
> * プロキシ サーバーを [信頼できる宛先のリスト] に含める必要があるかどうかを確認します。 その場合は、必ず[このようなドメイン](./data-movement-security-considerations.md#firewall-requirements-for-on-premisesprivate-network)を [信頼できる宛先のリスト] に含めます。
> * SSL/TLS 証明書 "wu2.frontend.clouddatahub.net/" がプロキシ サーバー上で信頼されているかどうかを確認します。
> * プロキシで Active Directory 認証を使用している場合は、サービス アカウントを、"Integration Runtime サービス" としてプロキシにアクセスできるユーザー アカウントに変更してください。

### <a name="error-message-self-hosted-integration-runtime-nodelogical-self-hosted-ir-is-in-inactive-running-limited-state"></a>エラー メッセージ:セルフホステッド統合ランタイム ノードまたは論理セルフホステッド IR は非アクティブまたは "実行中 (制限あり)" 状態です。

#### <a name="cause"></a>原因 

次のスクリーンショットに示すように、セルフホステッド統合ランタイム ノードは **[非アクティブ]** 状態になる場合があります。

![非アクティブ状態のセルフホステッド統合ランタイム ノードのスクリーンショット](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

この動作は、ノードが相互に通信できない場合に発生します。

#### <a name="resolution"></a>解決方法

1. ノードでホストされている仮想マシン (VM) にログインします。 **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** で、イベント ビューアーを開き、エラー ログをフィルター処理します。

1. エラー ログに次のエラーが含まれているかどうかを確認します。 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. このエラーが表示された場合は、コマンド プロンプト ウィンドウで次のコマンドを実行します。 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 次のスクリーンショットに示されている "Could not open connection to the host (ホストへの接続を開けませんでした)" というコマンドライン エラーが表示された場合は、IT 部門に連絡して、この問題を解決するための支援を要請してください。 telnet を正常に実行できた後も、統合ランタイム ノードの状態に問題がある場合は、Microsoft サポートにお問い合わせください。
        
   !["Could not open connection to the host (ホストへの接続を開けませんでした)" というコマンドライン エラーのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. エラー ログに次のエントリが含まれているかどうかを確認します。

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. この問題を解決するには、次の方法のいずれかまたは両方を試してください。
    - すべてのノードを同じドメインに配置する。
    - ホストされているすべての VM のホスト ファイルで、IP と ホストのマッピングを追加する。

### <a name="connectivity-issue-between-the-self-hosted-ir-and-your-data-factory-instance-or-the-self-hosted-ir-and-the-data-source-or-sink"></a>セルフホステッド IR とデータ ファクトリ インスタンス、またはセルフホステッド IR とデータ ソースまたはシンク間の接続の問題

ネットワーク接続の問題のトラブルシューティングを行うには、ネットワーク トレースの収集方法を確認して、その使用方法を理解し、[Microsoft ネットワーク モニター (Netmon) トレースを分析](#analyze-the-netmon-trace)してから、実際のケースでセルフホステッド IR から Netmon ツールを適用する必要があります。

#### <a name="symptoms"></a>現象

次のスクリーンショットに示すようにセルフホステッド IR とデータ ファクトリ インスタンス間、またはセルフホステッド IR とデータ ソースまたはシンク間の特定の接続問題に関するトラブルシューティングが必要になる場合があります。 

!["Processed HTTP request failed (処理された HTTP 要求が失敗しました)" というメッセージのスクリーンショット](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

どちらのインスタンスでも、次のエラーが発生する可能性があります。

* "Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server:'IP address' (エラーによってコピーが失敗しました: Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException、Message= SQL Server に接続できません: 'IP address')"

* "One or more errors occurred. (1 つ以上のエラーが発生しました。) この要求の送信中にエラーが発生しました。 基になる接続が閉じられました。An unexpected error occurred on a receive. (受信時に予期しないエラーが発生しました。) データをトランスポート接続から読み取れません: リモート ホストによって、既存の接続は強制的に切断されました。 An existing connection was forcibly closed by the remote host Activity ID. (既存の接続はリモート ホストのアクティビティ ID によって強制的に切断されました。)"

#### <a name="resolution"></a>解決方法

上記のエラーが発生した場合は、このセクションの手順に従ってトラブルシューティングを行います。

- 分析のために Netmon トレースを収集します。 

    1. サーバーからクライアント側へのリセットを確認するためのフィルターを設定できます。 次のスクリーンショットの例では、サーバー側が Data Factory サーバーであることを確認できます。

        ![Data Factory サーバーのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

    1. リセット パッケージを取得したら、伝送制御プロトコル (TCP) を追跡してメッセージ交換を見つけることができます。

        ![TCP メッセージ交換のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

    1. フィルターを削除して、下のクライアントと Data Factory サーバー間のメッセージ交換を取得できます。

        ![メッセージ交換の詳細のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)

- 収集した Netmon トレースの分析では、Time to Live (TTL) の合計が 64 であることが示されています。 [IP Time to Live (TTL) とホップ制限の基本](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)に関する記事 (次の一覧に抜粋) に記載されている値によると、パッケージをリセットし、切断を発生させたのは Linux システムであることがわかります。

    既定の TTL とホップの制限値は、こちらの一覧に示すように、オペレーティング システムによって異なります。
    - Linux カーネル 2.4 (2001 年ごろ):TCP、ユーザー データグラム プロトコル (UDP)、およびインターネット制御メッセージ プロトコル (ICMP) の場合は 255
    - Linux カーネル 4.10 (2015):TCP、UDP、および ICMP の場合は 64
    - Windows XP (2001):TCP、UDP、および ICMP の場合は 128
    - Windows 10 (2015):TCP、UDP、および ICMP の場合は 128
    - Windows Server 2008:TCP、UDP、および ICMP の場合は 128
    - Windows Server 2019 (2018):TCP、UDP、および ICMP の場合は 128
    - MacOS (2001):TCP、UDP、および ICMP の場合は 64

    ![TTL 値 61 を示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    前の例では、TTL は 64 ではなく 61 と表示されています。これは、ネットワーク パッケージが宛先に到達するとき、ルーターやネットワーク デバイスなどのさまざまなホップを経由する必要があるためです。 最終的な TTL を算出するために、ルーターやネットワーク デバイスの数が差し引かれます。
    
    この場合は、リセットが Linux システムから TTL 64 で送信された可能性があることがわかります。

-  リセット デバイスの発信元を確認するには、セルフホステッド IR から 4 番目のホップを確認します。
 
    "*Linux システム A からの TTL 64 のネットワーク パッケージ -> B TTL 64-1 = 63 -> C TTL 63-1 = 62 -> TTL 62-1 = 61 セルフホステッド IR*"

- 理想的な状況では、TTL ホップ数は 128 になります。これは、Windows オペレーティング システムによってデータ ファクトリ インスタンスが実行されていることを意味します。 次の例に示すように、"*128-107 = 21 ホップ*" です。これは、TCP 3 ハンドシェイク中に、データ ファクトリ インスタンスからセルフホステッド IR にパッケージの 21 ホップが送信されたことを意味します。
 
    ![TTL 値 107 を示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    このため、セルフホステッド IR からの 4 番目のホップを確認するには、ネットワーク チームと協力する必要があります。 それがファイアウォールである場合は、Linux システムと同様に、TCP 3 ハンドシェイク後にそのデバイスによってパッケージがリセットされた理由をログで確認します。 
    
    調査する場所がわからない場合は、問題発生時のセルフホステッド IR とファイアウォール両方からの Netmon トレースを取得してみてください。 この方法は、どのデバイスによってパッケージがリセットされて切断が引き起こされた可能性があるかを判断するのに役立ちます。 この場合も、ネットワーク チームと協力して作業を進める必要があります。

### <a name="analyze-the-netmon-trace"></a>Netmon トレースを分析する

> [!NOTE] 
> 次の手順は、Netmon トレースに適用されます。 Netmon トレースは現在サポート対象外のため、Wireshark をこの目的に使用できます。

収集した Netmon トレースを使用して telnet **8.8.8.8 888** を実行しようとすると、次のスクリーンショットに示すトレースが表示されます。

!["Could not open connection to the host on port 888" (ポート 888 でホストへの接続を開くことができませんでした) というエラー メッセージが表示されたスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon トレースの説明を示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

前の図は、ポート **888** で **8.8.8.8** サーバー側に TCP 接続を確立できなかったことを示しています。そのため、そこに 2 つの **SynReTransmit** 追加パッケージがあるのを確認できます。 ソース **SELF-HOST2** は、最初のパッケージを使用して **8.8.8.8** に接続できなかったため、接続を試行し続けます。

> [!TIP]
> この接続を確立するには、次の解決策を試してください。
> 1. **[フィルターの読み込み]**  >  **[標準フィルター]**  >  **[アドレス]**  >  **[IPv4 アドレス]** を選択します。
> 1. フィルターを適用するには、**IPv4.Address == 8.8.8.8** を入力し、 **[適用]** を選択します。 これにより、ローカル マシンから宛先 **8.8.8.8** への通信を確認できます。

![フィルター アドレスを示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![その他のフィルター アドレスを示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

次の例では成功したシナリオを示します。 

- 問題なく telnet **8.8.8.8 53** を実行できる場合は、TCP 3 ハンドシェイクが正常に行われ、セッションは TCP 4 ハンドシェイクで終了します。

    ![成功した接続シナリオを示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![成功した接続シナリオの詳細を示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 前述の TCP 3 ハンドシェイクでは、次のワークフローが生成されます。

    ![TCP 3 ハンドシェイク ワークフローの図。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- セッションを完了するための TCP 4 ハンドシェイクは、次のワークフローで示されています。

    ![TCP 4 ハンドシェイクの詳細のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 ハンドシェイク ワークフローの図。](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 

### <a name="microsoft-email-notification-about-updating-your-network-configuration"></a>ネットワーク構成の更新に関する Microsoft 電子メール通知

次のような電子メール通知を受信することがあります。これは、2020 年 11 月 8 日までに Azure Data Factory で新しい IP アドレスでの通信を許可するようにネットワーク構成を更新することを推奨しています。

   ![ネットワーク構成の更新を求める Microsoft 電子メール通知のスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/email-notification.png)

#### <a name="determine-whether-this-notification-affects-you"></a>この通知が自分に影響があるものかどうかを判断する

この通知は、次のシナリオに適用されます。

##### <a name="scenario-1-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-premises-behind-a-corporate-firewall"></a>シナリオ 1:企業のファイアウォール内でオンプレミスで実行されているセルフホステッド統合ランタイムからの送信方向の通信

自分に影響があるかどうかを判断する方法:

- [IP アドレスに対するファイアウォールの構成と許可リストの設定](data-movement-security-considerations.md#firewall-configurations-and-allow-list-setting-up-for-ip-addresses)に関する記事で説明されている方法が使用される、完全修飾ドメイン名 (FQDN) に基づいたファイアウォール規則を定義している場合は、影響を "*受けません*"。

- 企業のファイアウォールで送信 IP の許可リストを明示的に有効にしている場合は、影響を "*受けます*"。

   影響を受ける場合の対処方法: 2020 年 11 月 8 日までにネットワーク構成を更新して最新のデータ ファクトリの IP アドレスを使用するように、ネットワーク インフラストラクチャ チームに通知します。 最新の IP アドレスをダウンロードするには、「[ダウンロード可能な JSON ファイルを使用してサービス タグを検出する](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)」に移動してください。

##### <a name="scenario-2-outbound-communication-from-a-self-hosted-integration-runtime-thats-running-on-an-azure-vm-inside-a-customer-managed-azure-virtual-network"></a>シナリオ 2: カスタマー マネージド Azure 仮想ネットワーク内の Azure VM で実行されているセルフホステッド統合ランタイムからの送信方向の通信

自分に影響があるかどうかを判断する方法:

- セルフホステッド統合ランタイムが含まれているプライベート ネットワークに送信ネットワーク セキュリティ グループ (NSG) 規則があるかどうかを確認します。 送信制限がない場合は、影響を受けません。

- アウトバウンド規則の制限がある場合は、サービス タグを使用しているかどうかを確認します。 サービス タグを使用している場合は、影響を受けません。 新しい IP 範囲は既存のサービス タグの下にあるため、変更や追加を行う必要はありません。 

  ![DataFactory が宛先として表示されている宛先チェックのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/destination-check.png)

- Azure 仮想ネットワークの NSG 規則設定で送信 IP アドレスの許可リストを明示的に有効にしている場合は、影響を "*受けます*"。

   影響を受ける場合の対処方法: 2020 年 11 月 8 日までに Azure 仮想ネットワーク構成の NSG 規則を更新して最新のデータ ファクトリの IP アドレスを使用するように、ネットワーク インフラストラクチャ チームに通知します。 最新の IP アドレスをダウンロードするには、「[ダウンロード可能な JSON ファイルを使用してサービス タグを検出する](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)」に移動してください。

##### <a name="scenario-3-outbound-communication-from-ssis-integration-runtime-in-a-customer-managed-azure-virtual-network"></a>シナリオ 3: カスタマー マネージド Azure 仮想ネットワークでの SSIS Integration Runtime からの送信方向の通信

自分に影響があるかどうかを判断する方法:

- SQL Server Integration Services (SSIS) Integration Runtime が含まれているプライベート ネットワークに送信 NSG 規則があるかどうかを確認します。 送信制限がない場合は、影響を受けません。

- アウトバウンド規則の制限がある場合は、サービス タグを使用しているかどうかを確認します。 サービス タグを使用している場合は、影響を受けません。 新しい IP 範囲は既存のサービス タグの下にあるため、変更や追加を行う必要はありません。

- Azure 仮想ネットワークの NSG 規則設定で送信 IP アドレスの許可リストを明示的に有効にしている場合は、影響を "*受けます*"。

  影響を受ける場合の対処方法: 2020 年 11 月 8 日までに Azure 仮想ネットワーク構成の NSG 規則を更新して最新のデータ ファクトリの IP アドレスを使用するように、ネットワーク インフラストラクチャ チームに通知します。 最新の IP アドレスをダウンロードするには、「[ダウンロード可能な JSON ファイルを使用してサービス タグを検出する](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)」に移動してください。

### <a name="couldnt-establish-a-trust-relationship-for-the-ssltls-secure-channel"></a>SSL/TLS のセキュリティで保護されているチャネルに対して信頼関係を確立できなかった 

#### <a name="symptoms"></a>現象

セルフホステッド IR は Azure Data Factory サービスに接続できませんでした。

セルフホステッド IR のイベント ログまたは CustomLogEvent テーブル内のクライアント通知ログを確認すると、次のエラー メッセージがあります。

"基になる接続が閉じられました: SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。 検証プロシージャによると、リモート証明書は無効です。"

Data Factory サービスのサーバー証明書を確認する最も簡単な方法は、ブラウザーで Data Factory サービスの URL を開くことです。 たとえば、セルフホステッド IR がインストールされているマシンで[サーバー証明書の確認リンク](https://eu.frontend.clouddatahub.net/)を開き、サーバー証明書情報を表示します。

  ![Azure Data Factory サービスの [check server certificate]\(サーバー証明書の確認\) ペインのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/server-certificate.png)

  ![サーバー証明のパスを確認するためのウィンドウのスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-path.png)

#### <a name="cause"></a>原因

この問題の原因は 2 つ考えられます。

- 理由 1: Data Factory サービスのサーバー証明書のルート CA が、セルフホステッド IR がインストールされているマシンで信頼されていません。 
- 理由 2: お使いの環境でプロキシを使用しており、Data Factory サービスのサーバー証明書がプロキシによって置き換えられていますが、置き換えられたサーバー証明書が、セルフホステッド IR がインストールされているマシンで信頼されていません。

#### <a name="resolution"></a>解決方法

- 理由 1 の場合: Data Factory サーバー証明書とその証明書チェーンが、セルフホステッド IR がインストールされているマシンによって信頼されていることを確認してください。
- 理由 2 の場合: セルフホステッド IR マシン上の置き換えられたルート CA を信頼するか、Data Factory サーバー証明書を置き換えないようにプロキシを構成します。

Windows での証明書の信頼の詳細については、[信頼されたルート証明書のインストール](/skype-sdk/sdn/articles/installing-the-trusted-root-certificate)に関する記事を参照してください。

#### <a name="additional-information"></a>関連情報
DigiCert から署名されている新しい SSL 証明書がロールアウトされました。 DigiCert Global Root G2 が、信頼されたルート CA にあるかどうかを確認してください。

  ![[信頼されたルート証明機関] ディレクトリ内の [DigiCert Global Root G2] フォルダーを示すスクリーンショット。](media/self-hosted-integration-runtime-troubleshoot-guide/trusted-root-ca-check.png)

信頼されたルート CA に含まれていない場合は、[こちらからダウンロード](http://cacerts.digicert.com/DigiCertGlobalRootG2.crt )してください。 


## <a name="self-hosted-ir-sharing"></a>セルフホステッド IR の共有

### <a name="sharing-a-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>異なるテナントからのセルフホステッド IR の共有がサポートされない 

#### <a name="symptoms"></a>現象

Azure Data Factory の UI からセルフホステッド IR を共有しようとしているときに、(異なるテナントにある) 他のデータ ファクトリに気付くことがありますが、異なるテナントにあるデータ ファクトリ間でそれを共有することはできません。

#### <a name="cause"></a>原因

複数のテナントにまたがってセルフホステッド IR を共有することはできません。

## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A ページ](/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
