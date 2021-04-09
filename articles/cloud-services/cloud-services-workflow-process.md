---
title: Windows Azure VM のアーキテクチャのワークフロー | Microsoft Docs
description: この記事では、サービスをデプロイするときのワークフロー プロセスの概要を示します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 606510940460db963a2aa63deb57b6dba77de3ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700135"
---
# <a name="workflow-of-windows-azure-classic-vm-architecture"></a>Windows Azure クラシック VM のアーキテクチャのワークフロー 

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

この記事では、仮想マシンなどの Azure リソースをデプロイまたは更新するときに発生するワークフロー プロセスの概要を示します。 

> [!NOTE]
>Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。Resource Manager とクラシックです。 この記事では、クラシック デプロイ モデルの使用方法について説明します。

次の図は、Azure リソースのアーキテクチャを表しています。

:::image type="content" source="./media/cloud-services-workflow-process/workflow.jpg" alt-text="<alt Azure ワークフローに関するイメージ>":::

## <a name="workflow-basics"></a>ワークフローの基本
   
**A**. RDFE/FFE は、ユーザーからファブリックへの通信パスです。 RDFE (RedDog Front End) は、管理ポータルに対するフロントエンドであるパブリックに公開された API、および Visual Studio や Azure MMC などの Service Management API です。  ユーザーからのすべての要求は、RDFE を経由します。 FFE (Fabric Front End) は、RDFE からファブリック コマンドに要求を変換するレイヤーです。 RDFE からのすべての要求は、FFE を経由してファブリック コントローラーに到達します。

**B**. ファブリック コントローラーは、データ センター内のすべてのリソースの管理と監視を担当します。 ゲスト OS のバージョン、サービス パッケージ、サービスの構成、サービスの状態などの情報を送信するファブリック OS 上のファブリック ホスト エージェントと通信します。

**C**. ホスト エージェントはホスト OS 上に存在し、意図される目標の状態に向けてロールを更新し、ゲスト エージェントとのハートビートのチェックを行うために、ゲスト OS のセットアップおよびゲスト エージェント (WindowsAzureGuestAgent) との通信を担当します。 ホスト エージェントは、ハートビート応答を 10 分間受信しなかった場合、ゲスト OS を再起動します。

**C2**. WaAppAgent は、WindowsAzureGuestAgent.exe のインストール、構成、および更新を担当します。

**D**.  WindowsAzureGuestAgent は、以下のことを担当します。

1. ファイアウォール、ACL、LocalStorage リソース、サービス パッケージと構成、証明書を含むゲスト OS の構成。
2. ロールを実行するユーザー アカウントの SID の設定。
3. ファブリックへのロールの状態の通信。
4. WaHostBootstrapper の開始、およびロールが目標の状態であることを確認するための監視。

**E**. WaHostBootstrapper は、以下のことを担当します。

1. ロールの構成の読み取り、およびロールを構成して実行するためのすべての適切なタスクとプロセスの開始。
2. すべての子プロセスの監視。
3. ロール ホスト プロセスでの StatusCheck イベントの生成。

**F**. ロールが完全な IIS Web ロールとして構成されている場合は、IISConfigurator が実行されます。 以下の処理を担当します。

1. 標準 IIS サービスの開始
2. Web 構成での書き換えモジュールの構成
3. サービス モデルの構成済みロールに対する AppPool の設定
4. DiagnosticStore LocalStorage フォルダーを指すように IIS ログを設定
5. アクセス許可と ACL の構成
6. Web サイトは %roleroot%:\sitesroot\0 に存在し、AppPool は IIS を実行するためにこの場所を参照します。 

**G**. スタートアップ タスクは、ロール モデルによって定義されており、WaHostBootstrapper によって開始されます。 スタートアップ タスクはバックグラウンドで非同期に実行するように構成でき、ホスト ブートストラッパーはスタートアップ タスクを開始してから、他のスタートアップ タスクに進みます。 スタートアップ タスクは、簡易 (既定) モードで実行するように構成することもできます。このモードでは、ホスト ブートストラッパーは、スタートアップ タスクの実行が完了するまで待ってから、成功 (0) 終了コードを返した後で、次のスタートアップ タスクを続けます。

**H**. これらのタスクは、SDK の一部であり、ロールのサービス定義 (.csdef) でプラグインとして定義されています。 スタートアップ タスクに展開されるとき、**DiagnosticsAgent** と **RemoteAccessAgent** は、2 つのスタートアップ タスクが定義されている点が他とは異なります。1 つは通常のもので、もう 1 つは **/blockStartup** パラメーターを含みます。 通常のスタートアップ タスクはバックグラウンド スタートアップ タスクとして定義されているので、ロール自体の実行中にバックグラウンドで実行できます。 **/blockStartup** のスタートアップ タスクは、簡易スタートアップ タスクとして定義されているので、WaHostBootstrapper はそれが終了するまで待ってから続行します。 **/blockStartup** タスクは、通常のタスクの初期化が終了するのを待った後で終了し、ホスト ブートストラッパーが続行できるようにします。 これは、ロール プロセスが開始する (これは、/blockStartup タスクによって行われます) 前に、診断と RDP アクセスを構成できるようにするためです。 また、これにより、診断と RDP アクセスは、ホスト ブートストラッパーがスタートアップ タスクを終了した (これは、通常タスによって行われます) 後も、実行を続けることができます。

**I**. WaWorkerHost は、通常の worker ロール用の標準ホスト プロセスです。 このホスト プロセスでは、ロールのすべての DLL と、OnStart や Run などのエントリ ポイントのコードがホストされています。

**J**. WaIISHost は、完全な IIS を使用する Web ロールに対するロール エントリ ポイント コード用のホスト プロセスです。 このプロセスでは、**RoleEntryPoint** クラスを使用する DLL で最初に見つかったものが読み込まれ、このクラスからコードが実行されます (OnStart、Run、OnStop)。 RoleEntryPoint クラス内で作成されるすべての **RoleEnvironment** イベント (StatusCheck や Changed など) は、このプロセスで発生します。

**K**. W3WP は標準の IIS ワーカー プロセスであり、ロールが完全な IIS を使うように構成されている場合に使われます。 これは、IISConfigurator から構成された AppPool を実行します。 ここで作成されるすべての RoleEnvironment イベント (StatusCheck や Changed など) は、このプロセスで発生します。 両方のプロセスでイベントをサブスクライブしている場合、RoleEnvironment イベントは両方の場所 (WaIISHost と w3wp.exe) で発生することに注意してください。

## <a name="workflow-processes"></a>ワークフローのプロセス

1. ユーザーは、".cspkg" ファイルや ".cscfg "ファイルのアップロード、リソースへの停止通知、構成の変更など、要求を行います。 これは、Azure portal で、または Visual Studio の発行機能のような Service Management API を使うツールによって、行うことができます。 この要求は、RDFE に送られてサブスクリプション関連のすべての処理が行われた後、FFE に送られます。 ワークフローのこれらの手順の残りの部分では、新しいパッケージをデプロイして開始します。
2. FFE で (アフィニティ グループや地理的な場所などのお客様からの入力に加えて、マシンの可用性などのファブリックからの入力に基づいて) 適切なマシン プールが検索され、そのマシン プール内のマスター ファブリック コントローラーとの通信が行われます。
3. ファブリック コントローラーで、使用可能な CPU コアを持つホストが検索されます (または、新しいホストが起動されます)。 サービス パッケージと構成がホストにコピーされ、ファブリック コントローラーとホスト OS 上のホスト エージェントとの間で通信が行われて、パッケージがデプロイされます (DIP、ポート、ゲスト OS などが構成されます)。
4. ホスト エージェントによって、ゲスト OS が開始され、ゲスト エージェント (WindowsAzureGuestAgent) との通信が行われます。 ホストによって、ゲストにハートビートが送信され、ロールがその目標とする状態に向かって動作していることが確認されます。
5. WindowsAzureGuestAgent によって、ゲスト OS (ファイアウォール、ACL、LocalStorage など) が設定され、新しい XML 構成ファイルが c:\Config にコピーされてから、WaHostBootstrapper プロセスが開始されます。
6. 完全な IIS Web ロールの場合、WaHostBootstrapper によって IISConfigurator が開始され、Web ロールの既存の AppPool を IIS から削除するように指示されます。
7. WaHostBootstrapper によって、**スタートアップ** タスクが E:\RoleModel.xml から読み取られて、スタートアップ タスクの実行が開始されます。 WaHostBootstrapper は、すべての簡易スタートアップ タスクが完了して、"成功" メッセージが返されるまで待機します。
8. 完全な IIS Web ロールの場合、WaHostBootstrapper によって、IIS AppPool を構成してサイトで `E:\Sitesroot\<index>` をポイントすることを指示する通知が IISConfigurator に送られます。`<index>` は、サービスに対して定義されている `<Sites>` 要素の 0 から始まるインデックスの番号です。
9. WaHostBootstrapper によって、ロールの種類に応じたホスト プロセスが開始されます。
    1. **worker ロール**: WaWorkerHost.exe が開始されます。 WaHostBootstrapper によって OnStart() メソッドが実行されます。 それが終了した後、WaHostBootstrapper によって Run() メソッドの実行が開始されるのと同時に、ロールが準備完了としてマークされて、ロード バランサーのローテーションに入れられます (InputEndpoints が定義されている場合)。 その後、WaHostBootsrapper はロールの状態をチェックするループに入ります。
    2. **完全な IIS Web ロール**: aIISHost が開始されます。 WaHostBootstrapper によって OnStart() メソッドが実行されます。 それが終了した後、Run() メソッドの実行が開始され、同時に、ロールが準備完了としてマークされて、ロード バランサーのローテーションに入れられます。 その後、WaHostBootsrapper はロールの状態をチェックするループに入ります。
10. 完全な IIS Web ロールへの Web 要求の着信でトリガーされた IIS により、W3WP プロセスが開始されて、要求の処理が行われます。これは、オンプレミスの IIS 環境の場合と同様です。

## <a name="log-file-locations"></a>ログ ファイルの場所

**WindowsAzureGuestAgent**

- C:\Logs\AppAgentRuntime.Log。  
このログには、開始、停止、新しい構成など、サービスへの変更が含まれます。 サービスが変更されない場合、このログ ファイルには時間の大きなギャップがあるものと予想されます。
- C:\Logs\WaAppAgent.Log。  
このログは、状態の更新とハートビート通知が含まれ、2 - 3 秒ごとに更新されます。  このログにはインスタンスの状態の履歴ビューが含まれ、インスタンスが準備完了状態ではなかったときがわかります。
 
**WaHostBootstrapper**

`C:\Resources\Directory\<deploymentID>.<role>.DiagnosticStore\WaHostBootstrapper.log`
 
**WaIISHost**

`C:\Resources\Directory\<deploymentID>.<role>\WaIISHost.log`
 
**IISConfigurator**

`C:\Resources\Directory\<deploymentID>.<role>\IISConfigurator.log`
 
**IIS ログ**

`C:\Resources\Directory\<guid>.<role>.DiagnosticStore\LogFiles\W3SVC1`
 
**Windows イベント ログ**

`D:\Windows\System32\Winevt\Logs`
