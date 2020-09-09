---
title: Azure Data Factory でセルフホステッド統合ランタイムのトラブルシューティングを行う
description: Azure Data Factory でセルフホステッド統合ランタイムの問題をトラブルシューティングする方法について説明します。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: abnarain
ms.openlocfilehash: 49d173e0d0f2b96c385b4325335483d25e9a7c2d
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800715"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のセルフホステッド統合ランタイムの一般的なトラブルシューティング方法について説明します。

## <a name="gather-self-hosted-ir-logs-from-azure-data-factory"></a>Azure Data Factory からのセルフホステッド IR ログの収集

セルフホステッド IR/共有 IR で実行したアクティビティが失敗した場合、Azure Data Factory ではエラー ログを表示してアップロードすることができます。 以下の手順に従ってエラー レポート ID を取得したら、レポート ID を入力して関連する既知の問題を特定できます。

1. **[アクティビティの実行]** ページに移動します。

1. **[エラー]** 列の下にあるボタンをクリックします。

    ![[アクティビティの実行] ページ](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 失敗したアクティビティの実行に関連するログが表示されます。 支援が必要な場合は、 **[ログの送信]** ボタンをクリックしてください。

    ![[ログの送信]](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 送信するログを選択できます。 *セルフホステッド IR* では、失敗したアクティビティに関連するログ、またはセルフホステッド IR ノードのすべてのログをアップロードできます。 *共有 IR* では、失敗したアクティビティに関連するログのみをアップロードできます。

    ![ログの選択](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 問題解決のためにさらに支援が必要な場合は、ログをアップロードする際に、レポート ID を記録しておきます。

    ![ログのアップロード](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> ログの表示とアップロードの要求は、すべてのオンライン セルフホステッド IR インスタンスで実行されます。 ログが見つからない場合に備えて、すべてのセルフホステッド IR インスタンスがオンラインになっていることを確認してください。 


## <a name="self-hosted-ir-general-failure-or-error"></a>セルフホステッド IR の一般的な障害またはエラー

### <a name="tlsssl-certificate-issue"></a>TLS/SSL 証明書の問題

#### <a name="symptoms"></a>現象

**[Self-hosted IR Configuration Manager]\(セルフホステッド IR 構成マネージャー\)**  ->  **[イントラネットからのリモート アクセス]** から TLS/SSL 証明書 (詳細) を有効にしようとすると、TLS/SSL 証明書を選択した後に、次のエラーが表示されます。

`Remote access settings are invalid. Identity check failed for outgoing message. The expected DNS identity of the remote endpoint was ‘abc.microsoft.com’ but the remote endpoint provided DNS claim ‘microsoft.com’. If this is a legitimate remote endpoint, you can fix the problem by explicitly specifying DNS identity ‘microsoft.com’ as the Identity property of EndpointAddress when creating channel proxy.`

上のケースでは、最後の項目として "microsoft.com" を含む証明書をユーザーが使用しています。

#### <a name="cause"></a>原因

これは WCF の既知の問題です。WCF TLS/SSL 検証でチェックされるのは、SAN の最後の DNSName だけです。 

#### <a name="resolution"></a>解決方法

Azure Data Factory v2 セルフホステッド IR では、ワイルドカード証明書がサポートされます。 この問題は通常、SSL 証明書が正しくないことが原因で発生します。 SAN の最後の DNSName が有効である必要があります。 以下の手順に従って検証してください。 
1.  管理コンソールを開き、[証明書の詳細] から *[サブジェクト]* と *[サブジェクトの別名]* の両方をよく確認します。 たとえば、上のケースでは、 *[サブジェクトの別名]* の最後の項目 ("DNS Name= microsoft.com.com") が正しくありません。
2.  証明書発行会社に連絡し、間違った DNS 名を取り除いてください。

### <a name="concurrent-jobs-limit-issue"></a>コンカレント ジョブの制限の問題

#### <a name="symptoms"></a>現象

Azure Data Factory の UI からコンカレント ジョブの制限を引き上げようとすると、"*更新中*" のまま、いつまでも応答しない状態になります。
コンカレント ジョブの最大値が 24 に設定されていたので、ジョブの実行速度を上げるために、この数を増やそうと考えています。 入力できる最小値は 3 で、入力できる最大値は 32 です。 UI でこの値を 24 から 32 に増やして "*更新*" ボタンを押したところ、下図のように "*更新中*" でスタックしてしまいました。 最新の情報に更新した後も表示される値は 24 のまま変わらず、結局 32 には更新されませんでした。

![状態の更新中](media/self-hosted-integration-runtime-troubleshoot-guide/updating-status.png)

#### <a name="cause"></a>原因

この設定には制限があります。その値はコンピューターの論理コア数とメモリによって異なります。もっと小さい値 (24 など) に調整して、結果を確認してください。

> [!TIP] 
> - 論理コア数の詳細およびご利用のマシンの論理コア数の確認方法については、[こちらの記事](https://www.top-password.com/blog/find-number-of-cores-in-your-cpu-on-windows-10/)を参照してください。
> - math.log の計算方法について詳しくは、[こちらの記事](https://www.rapidtables.com/calc/math/Log_Calculator.html)を参照してください。


### <a name="self-hosted-ir-ha-ssl-certificate-issue"></a>セルフホステッド IR HA の SSL 証明書の問題

#### <a name="symptoms"></a>現象

セルフホステッド IR のワーク ノードから次のエラーがレポートされました。

`Failed to pull shared states from primary node net.tcp://abc.cloud.corp.Microsoft.com:8060/ExternalService.svc/. Activity ID: XXXXX The X.509 certificate CN=abc.cloud.corp.Microsoft.com, OU=test, O=Microsoft chain building failed. The certificate that was used has a trust chain that cannot be verified. Replace the certificate or change the certificateValidationMode. The revocation function was unable to check revocation because the revocation server was offline.`

#### <a name="cause"></a>原因

SSL/TLS ハンドシェイクに関連した症状に対応していると、証明書チェーンの検証に関連したいくつかの問題に遭遇することがあります。 

#### <a name="resolution"></a>解決方法

- 以下、X.509 証明書チェーン構築エラーをトラブルシューティングするための簡単で直感的な方法を紹介します。
 
    1. 検証が必要な証明書をエクスポートします。 [manage computer certificate]\(コンピューター証明書の管理\) に移動し、チェックする証明書を見つけて、 **[すべてのタスク]**  ->  **[エクスポート]** を右クリックします。
    
        ![タスクをエクスポートする](media/self-hosted-integration-runtime-troubleshoot-guide/export-tasks.png)

    2. エクスポートされた証明書をクライアント マシンにコピーします。 
    3. クライアント側で、CMD で以下のコマンドを実行します。 以下の *\<certificate path>* と *\<output txt file path>* のプレースホルダーは、対応するパスに置き換えてください。
    
        ```
        Certutil -verify -urlfetch    <certificate path>   >     <output txt file path> 
        ```

        次に例を示します。

        ```
        Certutil -verify -urlfetch c:\users\test\desktop\servercert02.cer > c:\users\test\desktop\Certinfo.txt
        ```
    4. output txt ファイルにエラーがあるかどうかを確認します。 エラーの概要は、このテキスト ファイルの最後にあります。

        次に例を示します。 

        ![エラーの概要](media/self-hosted-integration-runtime-troubleshoot-guide/error-summary.png)

        ログ ファイルの最後に次に示すようなエラーがない場合、クライアント マシンでは証明書チェーンが正常に構築されていると考えることができます。
        
        ![ログ ファイルにエラーがない](media/self-hosted-integration-runtime-troubleshoot-guide/log-file.png)      

- AIA、CDP、OCSP が証明書ファイルに構成されている場合、 より直感的な方法でチェックすることができます。
 
    1. この情報は、証明書の詳細をチェックすることで把握できます。
    
        ![証明書の詳細](media/self-hosted-integration-runtime-troubleshoot-guide/certificate-detail.png)
    1. 次のコマンドを実行します。 *\<certificate path>* プレースホルダーは、対応する証明書のパスに置き換えてください。
    
        ```
          Certutil   -URL    <certificate path> 
        ```
    1. **URL 取得ツール**が表示されます。 証明書の検証は、AIA、CDP、OCSP から **[取得]** ボタンをクリックして実行できます。

        ![[取得] ボタン](media/self-hosted-integration-runtime-troubleshoot-guide/retrieval-button.png)
 
        AIA からの証明書が "検証済み" で、なおかつ CDP または OCSP からの証明書が "検証済み" であれば、証明書チェーンを正常に構築できます。

        AIA、CDP を取得するときにエラーが発生した場合は、ネットワーク チームと連携して、クライアント マシンをターゲット URL に接続できる状態にしてください。 http パスまたは ldap パスのどちらかが検証できれば問題ありません。

### <a name="self-hosted-ir-could-not-load-file-or-assembly"></a>セルフホステッド IR がファイルまたはアセンブリを読み込めない

#### <a name="symptoms"></a>現象

`Could not load file or assembly 'XXXXXXXXXXXXXXXX, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`
 
次に例を示します。 

`Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified. Activity ID: 92693b45-b4bf-4fc8-89da-2d3dc56f27c3`

#### <a name="cause"></a>原因

プロセス モニターを使用すると、次の結果を確認できます。

[![プロセス モニター](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png)](media/self-hosted-integration-runtime-troubleshoot-guide/process-monitor.png#lightbox)

> [!TIP] 
> 以下のスクリーンショットのようにフィルターを設定できます。
> これは、**System.ValueTuple** という DLL が、GAC 関連フォルダーにも、*C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* にも、*C:\Program Files\Microsoft Integration Runtime\4.0\Shared* フォルダーにも存在しないことを表しています。
> 基本的に DLL は、まず *GAC* フォルダーから読み込まれ、次に *Shared* フォルダーから、最後に *Gateway* フォルダーから読み込まれます。 したがって、そのいずれかのパスに DLL を配置することで状況が改善する場合があります。

![フィルターを設定する](media/self-hosted-integration-runtime-troubleshoot-guide/set-filters.png)

#### <a name="resolution"></a>解決方法

**System.ValueTuple.dll** は *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway\DataScan* フォルダーにあります。 **System.ValueTuple.dll** を *C:\Program Files\Microsoft Integration Runtime\4.0\Gateway* フォルダーにコピーすれば問題は解決します。

その他のファイルやアセンブリについても、同じ方法で欠落の問題を解決できます。

#### <a name="more-information"></a>説明

System.ValueTuple.dll が *%windir%\Microsoft.NET\assembly* や *%windir%\assembly* にある理由は、それが .NET の動作であるためです。 

以下のエラーを見ると、そこに *System.ValueTuple* アセンブリが存在していないのは明らかです。 そのため、アプリケーションが *System.ValueTuple.dll* アセンブリをチェックしようとしたときに、この問題が発生します。
 
`<LogProperties><ErrorInfo>[{"Code":0,"Message":"The type initializer for 'Npgsql.PoolManager' threw an exception.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.TypeInitializationException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[{"Code":0,"Message":"Could not load file or assembly 'System.ValueTuple, Version=4.0.2.0, Culture=neutral, PublicKeyToken=XXXXXXXXX' or one of its dependencies. The system cannot find the file specified.","EventType":0,"Category":5,"Data":{},"MsgId":null,"ExceptionType":"System.IO.FileNotFoundException","Source":"Npgsql","StackTrace":"","InnerEventInfos":[]}]}]</ErrorInfo></LogProperties>`
 
GAC の詳細については、[こちらの記事](https://docs.microsoft.com/dotnet/framework/app-domains/gac)を参照してください。


### <a name="how-to-audit-self-hosted-ir-key-missing"></a>セルフホステッド IR キーの欠落を監査する方法

#### <a name="symptoms"></a>現象

セルフホステッド統合ランタイムがキーの欠落で突然オフラインになります。イベント ログには `Authentication Key is not assigned yet` というエラー メッセージが記録されます。

![認証キーの欠落](media/self-hosted-integration-runtime-troubleshoot-guide/key-missing.png)

#### <a name="cause"></a>原因

- セルフホステッド IR ノードまたは論理セルフホステッド IR がポータルから削除された。
- 完全アンインストールが実行された。

#### <a name="resolution"></a>解決方法

上記のどちらの原因も該当しない場合、 *%programdata%\Microsoft\Data Transfer\DataManagementGateway* フォルダーに移動し、**Configurations** という名前のファイルが削除されているかどうかを確認してください。 削除されている場合、だれがファイルを削除したのかを[こちら](https://www.netwrix.com/how_to_detect_who_deleted_file.html)の手順に従って監査できます。

![構成ファイルを確認する](media/self-hosted-integration-runtime-troubleshoot-guide/configurations-file.png)


### <a name="cannot-use-self-hosted-ir-to-bridge-two-on-premises-data-stores"></a>セルフホステッド IR を使用して 2 つのオンプレミス データ ストアの橋渡しができない

#### <a name="symptoms"></a>現象

ソースとターゲット両方のデータ ストアにセルフホステッド IR を作成した後、その 2 つの IR を接続してコピーを完了したいと考えています。 データ ストアが別々の VNET に構成されている場合や、ゲートウェイのメカニズムを認識しない場合、"*the driver of source cannot be found in destination IR (ソースのドライバーがターゲット IR に見つかりません)* " や "*the source cannot be accessed by the destination IR (ターゲット IR がソースにアクセスできません)* " などのエラーが発生します。
 
#### <a name="cause"></a>原因

セルフホステッド IR は、それぞれのデータ ストアにインストールする必要のあるクライアント エージェントとしてではなく、コピー アクティビティの中央ノードとして設計されています。
 
上記のケースでは、各データ ストアのリンクされたサービスを同じ IR で作成する必要があります。また、その IR は、ネットワークを通じて両方のデータ ストアにアクセスできなければなりません。 IR のインストール先がソース データ ストアであれ、ターゲット データ ストアであれ、第 3 のマシンであれ、リンクされたサービスが、異なる IR を使用して 2 つ作成され、同じコピー アクティビティで使用された場合、ターゲット IR が使用されるので、両方のデータ ストアのドライバーをターゲット IR マシンにインストールする必要があります。

#### <a name="resolution"></a>解決方法

ソースとターゲットの両方のドライバーをターゲット IR にインストールし、そこからソース データ ストアにアクセスできることを確認してください。
 
2 つのデータ ストア間のネットワークをトラフィックが通過できない場合 (それらが 2 つの VNET に構成されている場合など)、IR がインストールされていたとしても、1 回のアクティビティではコピーが完了しません。 その場合は、2 つの IR を使用して、2 つのコピー アクティビティをそれぞれの VNET に作成できます。一方の IR で、データ ストア 1 を Azure Blob Storage にコピーし、もう一方の IR で Azure Blob Storage からデータ ストア 2 にコピーします。 そうすることで、離れている 2 つのデータ ストアの橋渡しに IR を使用するという要件をシミュレートできます。


### <a name="credential-sync-issue-causes-credential-lost-from-ha"></a>資格情報の同期の問題によって HA から資格情報が失われる

#### <a name="symptoms"></a>現象

データ ソースの資格情報 "XXXXXXXXXX" は、Azure portal のリンク サービスを削除したときにペイロードの現在の Integration Runtime ノードから削除されたか、タスクのペイロードが正しくありません。自分の資格情報で新しいリンク サービスをもう一度作成してください。

#### <a name="cause"></a>原因

セルフホステッド IR が 2 つのノードから成る HA モードで作成されていますが、それらの間で資格情報が同期されていません。これは、ディスパッチャー ノードに格納されている資格情報が他のワーカー ノードと同期されていないことを意味します。 ディスパッチャー ノードからワーカー ノードへのフェールオーバーが発生したときに、以前のディスパッチャー ノードにしか資格情報が存在しないと、資格情報にアクセスを試みた時点でそのタスクは失敗し、上記のエラーが発生します。

#### <a name="resolution"></a>解決方法

2 つのノードの資格情報を同期状態にすることが、この問題を回避する唯一の方法となります。 それ以外の場合、新しいディスパッチャーの資格情報を再入力する必要があります。


### <a name="cannot-choose-the-certificate-due-to-private-key-missing"></a>秘密キーが存在しないため証明書を選択できません

#### <a name="symptoms"></a>現象

1.  証明書ストアに PFX ファイルをインポートします。
2.  IR Configuration Manager の UI で証明書を選択しようとすると、次のエラーが発生します。

    ![秘密キーがない](media/self-hosted-integration-runtime-troubleshoot-guide/private-key-missing.png)

#### <a name="cause"></a>原因

- ユーザー アカウントの特権が低く、秘密キーにアクセスできません。
- 証明書は署名としては生成されましたが、キー交換としては生成されていません。

#### <a name="resolution"></a>解決方法

1.  秘密キーにアクセスできる特権アカウントを使用して UI を操作します。
2.  次のコマンドを実行して証明書をインポートします。
    
    ```
    certutil -importpfx FILENAME.pfx AT_KEYEXCHANGE
    ```


## <a name="self-hosted-ir-setup"></a>セルフホステッド IR の設定

### <a name="the-integration-runtime-registration-error"></a>Integration Runtime の登録エラー 

#### <a name="symptoms"></a>現象

次のような理由から、セルフホステッド IR を別のアカウントで実行したい場合があります。
- サービス アカウントが会社のポリシーで許可されていない。
- なんらかの認証が必要である。

サービス パネルでサービス アカウントを変更した後、Integration Runtime の動作が停止していることを確認できます。

![IR 登録エラー](media/self-hosted-integration-runtime-troubleshoot-guide/ir-registration-error.png)

#### <a name="cause"></a>原因

サービス アカウントにしか許可されないリソースは数多く存在します。 サービス アカウントを別のアカウントに変更しても、すべての依存するリソースのアクセス許可は同じままです。

#### <a name="resolution"></a>解決方法

Integration Runtime のイベント ログにアクセスしてエラーを確認してください。

![IR イベント ログ](media/self-hosted-integration-runtime-troubleshoot-guide/ir-event-log.png)

上記の *UnauthorizedAccessException* としてエラーが表示された場合は、次の手順に従ってください。


1. Windows サービス パネルで *DIAHostService* ログオン サービス アカウントを確認します。

    ![ログオン サービス アカウント](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. ログオン サービス アカウントに、 *%programdata%\Microsoft\DataTransfer\DataManagementGateway* フォルダーに対する R/W アクセス許可があるかどうかを確認します。

    - サービスのログオン アカウントが変更されていなければ、既定で R/W のアクセス許可が割り当てられているはずです。

        ![サービスのアクセス許可](media/self-hosted-integration-runtime-troubleshoot-guide/service-permission.png)

    - サービスのログオン アカウントを変更した場合は、以下の手順に従って問題を軽減してください。
        1. 現在のセルフホステッド IR を完全にアンインストールします。
        1. セルフホステッド IR ビットをインストールします。
        1. 下記の手順に従ってサービス アカウントを変更します。 
            1. セルフホステッド IR のインストール フォルダーに移動し、*Microsoft Integration Runtime\4.0\Shared* フォルダーに切り替えます。
            1. 昇格された特権を使用してコマンド ラインを開始します。 *\<user>* と *\<password>* を実際のユーザー名とパスワードに置き換えて、次のコマンドを実行します。
                       
                ```
                dmgcmd.exe -SwitchServiceAccount "<user>" "<password>"
                ```
            1. LocalSystem アカウントに変更したい場合、そのアカウントの正しい形式を使用する必要があります。 以下に、正しい形式の例を示します。

                ```
                dmgcmd.exe -SwitchServiceAccount "NT Authority\System" ""
                ```         
                次のような形式は使用**しない**でください。

                ```
                dmgcmd.exe -SwitchServiceAccount "LocalSystem" ""
                ```              
            1. または、LocalSystem には管理者よりも高い特権があるため、[サービス] で直接それを変更することもできます。
            1. IR サービスのログオン アカウントには、ローカルまたはドメインのユーザーを使用できます。            
        1. Integration Runtime を登録します。

"*サービス 'Integration Runtime Service' (DIAHostService) が開始できませんでした。システム サービスを開始するための十分な特権があるか確認してください*" というエラーが表示された場合は、次の手順に従います。

1. Windows サービス パネルで *DIAHostService* ログオン サービス アカウントを確認します。
   
    ![ログオン サービス アカウント](media/self-hosted-integration-runtime-troubleshoot-guide/logon-service-account.png)

2. そのログオン サービス アカウントに、Windows サービスを開始するための **[サービスとしてログオン]** アクセス許可があるかどうかを確認します。

    ![サービスとしてログオン](media/self-hosted-integration-runtime-troubleshoot-guide/logon-as-service.png)

#### <a name="more-information"></a>説明

解決方法として、上記の 2 つのパターンのどちらも該当しない場合は、次の Windows イベント ログを収集してみてください。 
- [アプリケーションとサービス ログ] -> [Integration Runtime]
- [Windows ログ] -> [アプリケーション]

### <a name="cannot-find-register-button-to-register-a-self-hosted-ir"></a>セルフホステッド IR を登録するための [登録] ボタンが見つからない    

#### <a name="symptoms"></a>現象

セルフホステッド IR を登録しようとしているのですが、Configuration Manager の UI に **[登録]** ボタンが見つかりません。

![[登録] ボタンがない](media/self-hosted-integration-runtime-troubleshoot-guide/no-register-button.png)

#### <a name="cause"></a>原因

*Integration Runtime 3.0* のリリース以降、よりクリーンでセキュアな環境を実現するために、既存の Integration Runtime ノードの **[登録]** ボタンは削除されました。 オンラインかどうかにかかわらず、既に Integration Runtime に登録されているノードを別の Integration Runtime に再登録するには、以前のノードをアンインストールしたうえで、ノードをインストールして登録する必要があります。

#### <a name="resolution"></a>解決方法

1. コントロール パネルに移動して、既存の Integration Runtime をアンインストールします。

    > [!IMPORTANT] 
    > 以下のプロセスでは [はい] を選択します。 アンインストール プロセス中、データを維持しないでください。

    ![データの削除](media/self-hosted-integration-runtime-troubleshoot-guide/delete-data.png)

1. Integration Runtime のインストーラー MSI がない場合は、[ダウンロード センター](https://www.microsoft.com/en-sg/download/details.aspx?id=39717)にアクセスして、最新の Integration Runtime をダウンロードします。
1. MSI をインストールし、Integration Runtime を登録します。


### <a name="unable-to-register-the-self-hosted-ir-due-to-localhost"></a>localhost が原因でセルフホステッド IR を登録できない    

#### <a name="symptoms"></a>現象

get_LoopbackIpOrName 時、新しいマシンにセルフホステッド IR を登録できません。

**デバッグ:** 実行時エラーが発生しました。
'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' のタイプ初期化子が例外をスローしました。
データベースの参照中に、修復できないエラーが発生しました。
 
**例外の詳細:** System.TypeInitializationException: 'Microsoft.DataTransfer.DIAgentHost.DataSourceCache' のタイプ初期化子が例外をスローしました。 ---> System.Net.Sockets.SocketException:データベースの参照中に、System.Net.Dns.GetAddrInfo(String name) で修復できないエラーが発生しました。

#### <a name="cause"></a>原因

この問題は通常、localhost を解決するときに発生します。

#### <a name="resolution"></a>解決方法

Localhost 127.0.0.1 を使用してファイルをホストすれば、この問題は解決します。


### <a name="self-hosted-setup-failed"></a>セルフホステッドの設定に失敗した    

#### <a name="symptoms"></a>現象

既存の IR のアンインストール、新しい IR のインストール、既存の IR の新しい IR へのアップグレードができません。

#### <a name="cause"></a>原因

インストールは、Windows インストーラー サービスに依存します。 インストールの問題が生じる原因としては、さまざまな理由が考えられます。
- ディスク領域が足りない
- 必要な権限がない
- なんらかの理由で NT サービスがロックされている
- CPU 使用率が高すぎる
- ネットワークの速度が遅い場所で MSI ファイルがホストされている
- 一部のシステム ファイルまたはレジストリに意図しないアクセスがあった


## <a name="self-hosted-ir-connectivity-issues"></a>セルフホステッド IR の接続の問題

### <a name="self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>Self-hosted integration runtime can't connect to cloud service (セルフホステッド統合ランタイムはクラウド サービスに接続できません)

#### <a name="symptoms"></a>現象

![セルフホステッド IR の接続問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

セルフホステッド統合ランタイムが Data Factory サービス (バックエンド) に接続できません。 この問題は、通常、ファイアウォールのネットワーク設定によって発生します。

#### <a name="resolution"></a>解決方法

1. 統合ランタイム サービスが実行されているかどうかを確認します。
    
   ![セルフホステッド IR サービスの実行状態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. サービスが実行されている場合は、手順 3 に進みます。

1. セルフホステッド統合ランタイム (既定の設定) にプロキシが構成されていない場合は、セルフホステッド統合ランタイムがインストールされているマシン上で次の PowerShell コマンドを実行します。

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > サービスの URL は、Data Factory の場所によって変わる場合があります。 サービスの URL は、 **[ADF UI]**  >  **[接続]**  >  **[統合ランタイム]**  >  **[Edit Self-hosted IR]\(セルフホステッド IR の編集\)**  >  **[ノード]**  >  **[View Service URLs]\(サービスの URL の表示\)** で確認できます。
            
    想定される応答を次に示します。
            
    ![PowerShell コマンドの応答](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 想定される応答が得られない場合は、状況に応じて次のいずれかの方法を使用します。
            
    * "リモート名を解決できませんでした" というメッセージを受け取る場合は、ドメイン ネーム システム (DNS) の問題が発生しています。 この問題を解決するには、ネットワーク チームに問い合わせてください。
    * "ssl/tls cert is not trusted"(ssl/tls 証明書は信頼されていません) というメッセージを受け取る場合は、マシン上で https://wu2.frontend.clouddatahub.net/ の証明書が信頼されているかどうかを確認し、証明書マネージャーを使用して公開証明書をインストールします。 この操作により、問題が軽減されます。
    * **[Windows]**  >  **[イベント ビューアー (ログ)]**  >  **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** に移動し、DNS、ファイアウォール規則、または会社のネットワーク設定が原因でエラーが発生していないかを確認します (そのようなエラーが見つかった場合は、接続を強制的に閉じてください)。どのような企業でもネットワーク設定をカスタマイズしているため、ネットワーク チームに問い合わせてこのような問題を解決してください。

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
            
![PowerShell コマンドの応答 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> プロキシに関する考慮事項：
> *    プロキシ サーバーを [信頼できる宛先のリスト] に含める必要があるかどうかを確認します。 その場合は、必ず[このようなドメイン](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)を [信頼できる宛先のリスト] に含めます。
> *    TLS/SSL 証明書 "wu2.frontend.clouddatahub.net/" がプロキシ サーバー上で信頼されているかどうかを確認します。
> *    プロキシで Active Directory 認証を使用している場合は、サービス アカウントを、"Integration Runtime サービス" としてプロキシにアクセスできるユーザー アカウントに変更してください。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>エラー メッセージ:セルフホステッド統合ランタイム ノード/論理 SHIR が非アクティブ/ "実行中 (制限あり)" の状態です

#### <a name="cause"></a>原因 

セルフホステッド統合ランタイム ノードは、次のスクリーンショットに示すように、 **[非アクティブ]** 状態になる場合があります。

![非アクティブなセルフホステッド IR ノード](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

この動作は、ノードが相互に通信できない場合に発生します。

#### <a name="resolution"></a>解決方法

1. ノードでホストされている VM にログインします。 イベント ビューアーを開き、 **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** 以下のすべてのエラー ログをフィルター処理します。

1. エラー ログに次のエラーが含まれているかどうかを確認します。 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. このエラーが表示されている場合は、コマンド ラインで次を実行します。 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 次のエラーが発生した場合は、IT 部門に連絡して、この問題の解決の支援を依頼してください。 telnet が正常に実行された後も、引き続き統合ランタイム ノードの状態に問題がある場合は、Microsoft サポートに問い合わせてください。
        
   ![コマンド ライン エラー](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. エラー ログに次が含まれているかどうかを確認します。

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. この問題を解決するには、次の方法のいずれかまたは両方を試してください。
    - すべてのノードを同じドメインに配置する。
    - ホストされているすべての VM のホスト ファイルで、IP と ホストのマッピングを追加する。

### <a name="connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>セルフホステッド IR と Data Factory の間、またはセルフホステッド IR とデータソース (またはシンク) の間の接続に関する問題

ネットワーク接続の問題のトラブルシューティングを行うには、[ネットワーク トレースの収集](#how-to-collect-netmon-trace)方法を確認して、その使用方法を理解し、[Netmon トレースを分析](#how-to-analyze-netmon-trace)してから、実際のケースでセルフホステッド IR から Netmon ツールを適用する必要があります。

#### <a name="symptoms"></a>現象

接続の問題のトラブルシューティングを行う場合 (セルフホステッド IR と Data Factory 間、 

![HTTP 要求に失敗しました](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

またはセルフホステッド IR とデータソース/シンク間など)、次のエラーが発生することがあります。

`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

#### <a name="resolution"></a>解決策:

上記の問題が発生した場合は、次の手順に従ってトラブルシューティングを行うことができます。

Netmon トレースを取得して、さらに分析します。
- まず、フィルターを設定して、サーバーからクライアント側へのリセットをすべて表示することができます。 次の例では、サーバー側が Data Factory サーバーであることがわかります。

    ![Data factory サーバー](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- リセット パッケージを取得したら、TCP を追跡してメッセージ交換を見つけることができます。

    ![メッセージ交換の検索](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 次に、フィルターを削除することにより、次のクライアントと Data Factory サーバー間の変換を取得できます。

    ![メッセージ交換の取得](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 収集された netmon トレースに基づき、TTL (TimeToLive) の合計が 64 であることがわかります。 [この記事](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)に記載されている「**既定の TTL とホップの制限値**」(下に抜粋) によると、パッケージをリセットして切断を発生させたのは Linux システムであることがわかります。

    既定の TTL とホップの制限値は、オペレーティング システムによって異なります。既定値の一部を次に示します。
    - Linux カーネル 2.4 (2001 年ごろ):TCP、UDP、および ICMP の場合は 255
    - Linux カーネル 4.10 (2015):TCP、UDP、および ICMP の場合は 64
    - Windows XP (2001):TCP、UDP、および ICMP の場合は 128
    - Windows 10 (2015):TCP、UDP、および ICMP の場合は 128
    - Windows Server 2008:TCP、UDP、および ICMP の場合は 128
    - Windows Server 2019 (2018):TCP、UDP、および ICMP の場合は 128
    - MacOS (2001):TCP、UDP、および ICMP の場合は 64

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    ただし、上記の例では 64 ではなく 61 と表示されています。これは、ネットワーク パッケージが宛先に到達すると、ルーターやネットワーク デバイスなどのさまざまなホップを経由する必要があるためです。 最終的な TTL では、ルーター/ネットワーク デバイスの数が差し引かれます。
    この場合は、リセットが Linux システムから TTL 64 で送信された可能性があることがわかります。

- リセット デバイスの発信元を確認するには、セルフホステッド IR から 4 番目のホップを確認する必要があります。
 
    *Linux システム A からの TTL 64 のネットワーク パッケージ -> B TTL 64-1 = 63 -> C TTL 63-1 = 62 -> TTL 62-1 = 61 セルフホステッド IR*

- 理想的な状況では、TTL は 128 になります。これは、Windows システムで Data Factory が実行されていることを意味します。 次の例に示すように、*128 – 107 = 21 ホップ*になります。つまり、TCP 3 ハンドシェイク中に、そのパッケージに関して 21 ホップが Data Factory からセルフホステッド IR に送信されたことを意味します。
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    このため、セルフホステッド IR からの 4 番目のホップを確認するには、ネットワーク チームと協力する必要があります。 Linux システムのファイアウォールである場合は、TCP 3 ハンドシェイクの後にそのデバイスがパッケージをリセットした理由をログで確認します。 ただし、調査する場所がわからない場合は、セルフホステッド IR とファイアウォールから問題が発生したときの netmon トレースを取得して、どのデバイスによってこのパッケージがリセットされて切断されるのかを特定してください。 この場合も、ネットワーク チームと協力して作業を進める必要があります。

### <a name="how-to-collect-netmon-trace"></a>Netmon トレースを収集する方法

1.  [この Web サイト](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)から Netmon ツールをダウンロードし、サーバー マシン (問題が発生しているサーバー) とクライアント (セルフホステッド IR など) にインストールします。

2.  フォルダーを作成します (たとえば、*D:\netmon* というパスに作成します)。 ログを保存するための十分な領域があることを確認します。

3.  IP とポートの情報をキャプチャします。 
    1. コマンド プロンプトを起動します。
    2. [管理者として実行] を選択し、次のコマンドを実行します。
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Netmon トレース (ネットワーク パッケージ) をキャプチャします。
    1. コマンド プロンプトを起動します。
    2. [管理者として実行] を選択し、次のコマンドを実行します。
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. 次の 3 種類のコマンドを使用して、ネットワーク ページをキャプチャできます。
        - オプション A: ラウンドロビン ファイル コマンド (ファイルが 1 つだけキャプチャされ、古いログが上書きされます)。

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - オプション B: チェーン ファイル コマンド (200 MB に達すると、新しいファイルが作成されます)。
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - オプション C:スケジュール ファイル コマンド。

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Netmon トレースのキャプチャを停止するには、**Ctrl + C** を押します。
 
> [!NOTE]
> クライアント コンピューターでのみ netmon トレースを収集できる場合は、サーバーの IP アドレスを取得してトレースの分析に役立ててください。

### <a name="how-to-analyze-netmon-trace"></a>Netmon トレースを分析する方法

上記で収集された netmon トレースを使用して telnet **8.8.8.8 888** を実行しようとすると、次のトレースが表示されます。

![Netmon トレース 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon トレース 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

これは、ポート **888** で **8.8.8.8** のサーバー側に TCP 接続を確立できなかったことを意味し、2 つの **SynReTransmit** 追加パッケージが表示されています。 最初のパッケージでソース **SELF-HOST2** が **8.8.8.8**に接続できなかったため、接続の試行が継続されます。

> [!TIP]
> - **[フィルターの読み込み]**  ->  **[標準フィルター]**  ->  **[アドレス]**  ->  **[IPv4 アドレス]** をクリックします。
> - フィルターとして **IPv4.Address == 8.8.8.8** を入力し、 **[適用]** をクリックします。 その後は、ローカル コンピューターから宛先 **8.8.8.8** への通信のみが表示されます。

![フィルター アドレス 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![フィルター アドレス 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

次の例は、良好な場合のシナリオを示しています。 

- Telnet **8.8.8.8 53** が問題なく動作している場合は、TCP 3 ハンドシェイクが行われ、その後、TCP 4 ハンドシェイクでセッションが終了したことを確認できます。

    ![良好なシナリオの例 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![良好なシナリオの例 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 上記の TCP 3 ハンドシェイクに基づき、次のワークフローを確認できます。

    ![TCP 3 ハンドシェイクのワークフロー](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- セッションを完了するための TCP 4 ハンドシェイクとそのフローは、次のようになります。

    ![TCP 4 ハンドシェイク](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 ハンドシェイクのワークフロー](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="self-hosted-ir-sharing"></a>セルフホステッド IR の共有

### <a name="share-self-hosted-ir-from-a-different-tenant-is-not-supported"></a>異なるテナントのセルフホステッド IR を共有することはサポートされない 

#### <a name="symptoms"></a>現象

Azure Data Factory の UI からセルフホステッド IR を共有しようとしているとき、(別のテナントにある) 他のデータ ファクトリが目に留まる場合がありますが、異なるテナント上のデータ ファクトリにまたがってセルフホステッド IR を共有することはできません。

#### <a name="cause"></a>原因

複数のテナントにまたがってセルフホステッド IR を共有することはできません。


## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
