---
title: Service Fabric のバックアップと復元
description: Reliable Stateful services と Reliable Actors のバックアップを構成するためのサービスである Service Fabric のバックアップと復元に関する概念ドキュメントです。
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98791613"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Reliable Services と Reliable Actors をバックアップおよび復元する
Azure Service Fabric は高可用性プラットフォームであり、複数のノードに状態を複製し、その高可用性を維持します。  つまり、クラスター内の 1 つのノードに障害が発生した場合でも、サービスは引き続き利用できます。 このプラットフォームに組み込まれている冗長性で十分と考えられますが、(外部ストアに) サービスのデータをバックアップすることが望ましい場合もあります。

> [!NOTE]
> データ損失シナリオから回復できるように、データのバックアップと復元 (および期待どおりに動作することのテスト) は重要です。
> 

> [!NOTE]
> Reliable Stateful サービスと Reliable Actors のデータのバックアップを構成するには、[定期的なバックアップと復元](service-fabric-backuprestoreservice-quickstart-azurecluster.md)を使用することをお勧めします。 
> 


たとえば、次のような状況を防ぐために、サービスのデータをバックアップすることが望まれます。

- Service Fabric クラスター全体が永久に失われた場合。
- サービス パーティションのレプリカの過半数の完全な損失。
- 状態が誤って削除されたり、破損したりするなどの管理エラー。 これは、十分な権限を持つ管理者が誤ってサービスを削除した場合に起こる可能性があります。
- データの破損を引き起こすサービスのバグ。 これは、サービス コードのアップグレードにより、Reliable Collection に不正なデータが書き込まれ始めると起こる可能性があります。 このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。
- オフライン データ処理 データを生成するサービスと切り離されて行われるビジネス インテリジェンスのために、データのオフライン処理を用意しておけば便利です。

バックアップ/復元機能では、 Reliable Services API を基盤とするサービスでバックアップを作成し、復元できます。 このプラットフォームで提供されるバックアップ API では、読み書き操作をブロックせずに、サービス パーティションの状態をバックアップできます。 復元 API では、選択したバックアップからサービス パーティションの状態を復元できます。

## <a name="types-of-backup"></a>バックアップの種類
バックアップには、2 つの選択肢があります: 完全と増分です。
完全バックアップは、レプリカの状態を再作成するために必要なすべてのデータ (チェックポイントとすべてのログ レコード) を含むバックアップです。
チェックポイントとログが含まれるため、完全バックアップはそれだけで復元できます。

完全バックアップでは、チェックポイントが大きいと問題が発生します。
たとえば、16 GB の状態が存在するレプリカでは、最大で約 16 GB のチェックポイントが追加されます。
目標復旧時点が 5 分の場合、レプリカを 5 分ごとにバックアップする必要があります。
バックアップを実行するたびに、50 MB (`CheckpointThresholdInMB` で構成可能) のログに加えて、16 GB のチェックポイントをコピーする必要があります。

![完全バックアップの例。](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

この問題の対応策として、増分バックアップがあります。増分バックアップには、前回のバックアップ以降の変更ログ レコードのみが含まれます。

![増分バックアップの例。](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

増分バックアップに含まれるのは前回のバックアップ以降の変更だけなので (チェックポイントは含まれません)、処理は速くなる傾向がありますが、それだけでは復元できません。
増分バックアップを復元するには、バックアップ チェーン全体が必要です。
バックアップ チェーンは、完全バックアップから始まってそれ以降の連続する差分バックアップを含むバックアップのチェーンです。

## <a name="backup-reliable-services"></a>Reliable Services のバックアップ
サービスの作成者は、バックアップのタイミングと保管場所を完全に制御できます。

バックアップを開始するには、このサービスが継承済みメンバー関数 `BackupAsync` を呼び出す必要があります。  
バックアップはプライマリ レプリカ以外からは実行できず、書き込みステータスが付与されている必要があります。

次に示すように、`BackupAsync` は `BackupDescription` オブジェクトを取得します。このオブジェクトでは、完全バックアップまたは増分バックアップを指定できます。また、ローカルに作成されたバックアップ フォルダーを外部ストレージに移動する準備ができたら呼び出される、`Func<< BackupInfo, CancellationToken, Task<bool>>>` も指定できます。

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

増分バックアップを実行する要求は、`FabricMissingFullBackupException` で失敗する可能性があります。 この例外は、次のいずれかが発生したことを示します。

- レプリカがプライマリになったために完全バックアップを取得していないか、
- 最後のバックアップ以降のログ レコードの一部が切り捨てられているか、
- またはレプリカが `MaxAccumulatedBackupLogSizeInMB` 制限を超えたことを示します。

ユーザーは、`MinLogSizeInMB` または `TruncationThresholdFactor` を構成することにより、増分バックアップの実行可能性を高めることができます。
これらの値を大きくすると、レプリカあたりのディスク使用量が増加します。
詳細については、[Reliable Services の構成](service-fabric-reliable-services-configuration.md)に関するページを参照してください。

`BackupInfo` は、バックアップに関する情報 (ランタイムでバックアップが保存されたフォルダー (`BackupInfo.Directory`) の場所など) を提供します。 このコールバック関数では、`BackupInfo.Directory` を外部ストアまたは別の場所に移動できます。  この関数はまた、バックアップ フォルダーがそのターゲット フォルダーに移動したかどうかを示すブール値を返します。

次のコードは、`BackupCallbackAsync` メソッドを使用してバックアップを Azure Storage にアップロードする方法を示しています。

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

上記の例では、`ExternalBackupStore` は Azure Blob Storage とのインターフェイスとして使用されるサンプル クラスです。`UploadBackupFolderAsync` はフォルダーを圧縮して Azure Blob Store に配置するメソッドです。

以下の点に注意してください。

  - 1 つのレプリカあたり同時に実行できるバックアップ操作は 1 つに限られます。 一度に複数の `BackupAsync` 呼び出しを行うと、`FabricBackupInProgressException` がスローされ、配信バックアップが 1 つに制限されます。
  - バックアップの途中でレプリカにエラーが発生した場合、バックアップは完了しないことがあります。 そのため、フェールオーバーが完了すると、サービスが必要に応じて `BackupAsync` を呼び出して、バックアップを再開する必要があります。

## <a name="restore-reliable-services"></a>Reliable Services の復元
通常、復元操作が必要になるケースは、次のカテゴリのいずれかに分類されます。

  - サービス パーティションのデータが失われた場合。 たとえば、パーティションの 3 つのレプリカのうちの 2 つのディスク (プライマリ レプリカを含む) が破損または消去された場合です。 新しいプライマリに、バックアップからデータを復元する必要がある場合。
  - サービス全体が失われた場合。 たとえば、管理者がサービス全体を削除したため、サービスとデータを復元する必要がある場合です。
  - サービスが、(たとえば、アプリケーションのバグに起因する) 破損したアプリケーション データを複製した場合。 その場合、サービスをアップグレードするか、元の状態に戻し、破損の原因を取り除き、破損していないデータを復元する必要があります。

さまざまな方法が可能ですが、ここでは `RestoreAsync` を使用して上記のシナリオから復元する例を紹介します。

## <a name="partition-data-loss-in-reliable-services"></a>Reliable Services でのパーティション データの損失
この場合、ランタイムが自動的にデータ損失を検出し、`OnDataLossAsync` API を呼び出します。

サービス作成者は、次を実行して回復する必要があります。

  - 仮想基本クラス メソッド `OnDataLossAsync` をオーバーライドします。
  - サービスのバックアップを含む外部の場所で最新のバックアップを見つけます。
  - 最新のバックアップをダウンロードします (およびそれが圧縮されている場合は、バックアップ フォルダーにバックアップを解凍します)。
  - `OnDataLossAsync` メソッドは、`RestoreContext` を提供します。 提供された `RestoreContext` で `RestoreAsync` API を呼び出します。
  - 復元が成功した場合は、true を返します。

`OnDataLossAsync` メソッドの実装例を次に示します。

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreContext.RestoreAsync` の呼び出しに渡される `RestoreDescription` には、`BackupFolderPath` という名前のメンバーが含まれます。
1 つの完全バックアップを復元するときは、この `BackupFolderPath` を完全バックアップが含まれるフォルダーのローカル パスに設定する必要があります。
完全バックアップと複数の増分バックアップを復元するときは、`BackupFolderPath` を、完全バックアップだけでなく、すべての増分バックアップも含まれるフォルダーのローカル パスに設定する必要があります。
提供された `BackupFolderPath` に完全バックアップが含まれていない場合、`RestoreAsync` の呼び出しで `FabricMissingFullBackupException` がスローされる可能性があります。
`BackupFolderPath` の増分バックアップのチェーンが壊れていると、`ArgumentException` もスローされる場合があります。
たとえば、完全バックアップと 1 番目および 3 番目の増分バックアップが含まれていて、2 番目の増分バックアップが含まれていないような場合です。

> [!NOTE]
> RestorePolicy は既定で「Safe」に設定されます。  つまり、このレプリカに含まれている状態と同じか、またはそれより古い状態がバックアップ フォルダーに含まれていることが検出された場合、`RestoreAsync` API は ArgumentException で失敗します。  `RestorePolicy.Force` を使用すると、この安全性チェックを省略できます。 これは `RestoreDescription` の一部として指定されます。
> 

## <a name="deleted-or-lost-service"></a>サービスの削除または損失
サービスが削除された場合、先にサービスを作成し直さないとデータを復元できません。  データを途切れなく復元するために、サービスを同じ構成 (パーティショニング スキームなど) で作成することが重要です。  サービスが起動したら、そのサービスのあらゆるパーティションで、データを復元する API (上記の `OnDataLossAsync`) を呼び出す必要があります。 その方法の 1 つは、すべてのパーティションで [FabricClient.TestManagementClient.StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) を使用することです。  

この点から、実装は上記のシナリオと同じになります。 各パーティションで、外部ストアから最新の関連バックアップを復元する必要があります。 ここで注意する点は、ランタイムが動的にパーティション ID を作成するため、パーティション ID が変更されている可能性があることです。 そのため、各パーティションで復元する正しい最新のバックアップを識別するために、サービスでパーティション情報とサービス名を保存する必要があります。

> [!NOTE]
> パーティションごとに `FabricClient.ServiceManager.InvokeDataLossAsync` を使用してサービス全体を復元することは、クラスターの状態が壊れる可能性があるので推奨されません。
> 

## <a name="replication-of-corrupt-application-data"></a>破損したアプリケーション データの複製
新しくデプロイされたアプリケーションのアップグレードにバグがある場合、データが破損することがあります。 たとえば、アプリケーションのアップグレードによって、Reliable Dictionary にあるすべての電話番号レコードが無効な局番で更新されることがあります。  そのような場合、Service Fabric は保存されるデータの性質を認識しないため、無効な電話番号が複製されます。

データを破損させるような重大なバグを検出した場合に最初にするべきことは、サービスをアプリケーション レベルで停止し、可能であれば、バグのないアプリケーション コードのバージョンにアップグレードします。  しかしながら、サービス コードを修正しても、データは壊れたままであり、復元する必要があります。  そのような場合、最新バックアップも壊れている可能性があるので、最新バックアップを復元するだけでは不十分です。  そのため、データが破損する前に行われた最後のバックアップを見つける必要があります。

どのバックアップが破損しているかわからない場合は、新しい Service Fabric クラスターをデプロイして、上記の「サービスの削除または損失」のシナリオと同様に、影響を受けたパーティションのバックアップを復元できます。  各パーティションで、最新から一番古いものの順でバックアップの復元を開始します。 破損していないバックアップを見つけたら、このパーティションの (そのバックアップより) 新しいすべてのバックアップを移動、削除します。 各パーティションでこの手順を繰り返します。 ここで、運用クラスターのパーティションで `OnDataLossAsync` が呼び出されたら、外部ストアで見つかった最後のバックアップが上記の手順で選択されたバックアップとなります。

ここで、「サービスの削除または損失」の手順を利用し、サービスの状態を、バグのあるコードが状態を破損させる前の状態に戻すことができます。

以下の点に注意してください。

  - 復元の際、復元されるバックアップがデータ損失前のパーティションの状態よりも古くなっている可能性があります。 そのため、復元は、可能な限り多くのデータを回復するための最後の手段としてのみ実行すべきです。
  - バックアップ フォルダー パスとバックアップ フォルダー内のファイルのパスを表す文字列は、FabricDataRoot パスや Application Type 名の長さによっては 255 文字を超える可能性があります。 これにより、`Directory.Move` などの一部の .NET メソッドで `PathTooLongException` 例外がスローされることがあります。 回避策の 1 つは、`CopyFile` などの kernel32 API を直接呼び出すことです。

## <a name="back-up-and-restore-reliable-actors"></a>Reliable Actors のバックアップと復元


Reliable Actors フレームワークは、Reliable Services 上に構築されています。 アクターをホストする ActorService は、ステートフル Reliable Service です。 そのため、Reliable Services で使用できるバックアップ機能と復元機能はすべて、Reliable Actors でも使用可能です (状態プロバイダーに固有の動作を除く)。 バックアップはパーティションごとに実行されるため、そのパーティション内のすべてのアクターがバックアップされます (復元も同様にパーティションごとに実行されます)。 バックアップ/復元を実行するには、サービスの所有者が、 ActorService クラスから派生するカスタム アクター サービス クラスを作成してから、前のセクションでの説明に従って Reliable Services と同様のバックアップ/復元を実行する必要があります。

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

カスタム アクター サービス クラスを作成する場合は、アクターの登録時にも、そのクラスを登録する必要があります。

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Reliable Actors の既定の状態プロバイダーは `KvsActorStateProvider` です。 増分バックアップは、`KvsActorStateProvider` については既定で無効になっています。 増分バックアップを有効にするには、コンストラクターで適切な設定を指定して `KvsActorStateProvider` を作成した後、次のコード スニペットに示すように、それを ActorService コンストラクターに渡します。

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

増分バックアップが有効になった後、増分バックアップを行うと、次のいずれかの理由によって FabricMissingFullBackupException が返され、処理が失敗することがあります。その場合は、増分バックアップを行う前に、完全バックアップを実行する必要があります。

  - レプリカがプライマリになったために完全バックアップを取得していない。
  - 前回のバックアップが行われた後に、ログ レコードの一部が切り捨てられた。

増分バックアップが有効になっている場合、`KvsActorStateProvider` は循環バッファーを使用して自身のログ レコードを管理することはせず、ログ レコードを定期的に切り捨てます。 ユーザーが 45 分間バックアップを行わなかった場合、システムは自動的にログ レコードを切り捨てます。 この間隔は、`KvsActorStateProvider` コンストラクターで `logTruncationIntervalInMinutes` を指定することによって構成できます (増分バックアップを有効にする場合と同様のやり方です)。 ログ レコードは、プライマリ レプリカの全データを送信して別のレプリカをビルドする必要がある場合にも、切り捨てられる可能性があります。

バックアップ チェーンから復元を行う際には、Reliable Services と同様、BackupFolderPath に複数のサブディレクトリを含める必要があります。その際、1 つのサブディレクトリに完全バックアップを含め、その他のサブディレクトリに増分バックアップを含めます。 バックアップ チェーンの検証が失敗した場合、復元 API は適切なエラー メッセージと共に FabricException をスローします。 

> [!NOTE]
> `KvsActorStateProvider` では現在、RestorePolicy.Safe オプションが無視されます。 この機能は、今後のリリースでサポートされる予定です。
> 

## <a name="testing-back-up-and-restore"></a>バックアップと復元のテスト
これは、重要なデータがバックアップされて復元できることを確認することは重要です。 そのためには、PowerShell で `Start-ServiceFabricPartitionDataLoss` コマンドレットを呼び出します。このコマンドレットは、サービスのデータのバックアップと復元の機能が予想どおりに動作するかどうかをテストするために、特定のパーティションでデータ損失を発生させることができます。  プログラムを使用してデータ損失を発生させし、そのイベントから復元することもできます。

> [!NOTE]
> GitHub で Web 参照アプリのバックアップおよび復元の機能の実装例を検索できます。 詳細については、`Inventory.Service` サービスをご覧ください。
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>具体的な内容: バックアップと復元の詳細
バックアップと復元の詳細を以下に示します。

### <a name="backup"></a>バックアップ
Reliable State Manager には、読み書き操作を中断することなく、一貫性のあるバックアップを作成する機能があります。 そのために、チェックポイントとログ永続化のメカニズムが活用されます。  Reliable State Manager は特定の時点でファジー (簡易) チェックポイントを作成し、トランザクション ログからの負荷を軽減し、復元時間を早めます。  `BackupAsync` が呼び出されると、最新のチェックポイント ファイルをローカル バックアップ フォルダーにコピーするように、Reliable State Manager がすべての Reliable Object に指示します。  次に、Reliable State Manager は "開始ポインター" から最新のログ レコードまですべてのログ レコードをバックアップ フォルダーにコピーします。  最新のログ レコードまでのすべてログ レコードがバックアップに含まれており、Reliable State Manager が先書きログを維持するため、コミットされた (`CommitAsync` が正常に制御を返した) すべてのトランザクションがバックアップに含まれていることが Reliable State Manager によって保証されます。

`BackupAsync` が呼び出された後にコミットするトランザクションは、バックアップに含まれていることもあれば、含まれていないこともあります。  プラットフォームによりローカルのバックアップ フォルダーにデータが入力されると (すなわち、ローカルのバックアップがランタイムにより完了すると)、サービスのバックアップ コールバックが呼び出されます。  このコールバックは、Azure Storage などの外部の場所にバックアップ フォルダーを移動する役割を担います。

### <a name="restore"></a>復元
Reliable State Manager には、`RestoreAsync` API を使用してバックアップから復元する機能があります。  
`RestoreContext` での `RestoreAsync` メソッドは、`OnDataLossAsync` メソッド内のみで呼び出すことができます。
`OnDataLossAsync` により返されるブール値は、サービスの状態が外部ソースから復元されたかどうかを示すものです。
`OnDataLossAsync` が true を返した場合、Service Fabric がこのプライマリから他のすべてのレプリカを再構築します。 Service Fabric は、`OnDataLossAsync` 呼び出しを受信するレプリカが最初にプライマリ ロールに切り替わっても、読み取り状態または書き込み状態にはならないことを保証します。
これは、StatefulService 実装側では、`OnDataLossAsync` が正常に完了するまで `RunAsync` が呼び出されないことを意味します。
次に、新しいプライマリで `OnDataLossAsync` が呼び出されます。
サービスが (true または false を返し) この API を完了し、関連再構成を完了するまで、API は 1 つずつ呼び出され続けます。

`RestoreAsync` は、まず、このメソッドが呼び出されたプライマリ レプリカで、既存の状態をすべて削除します。 次に、Reliable State Manager は、バックアップ フォルダーに存在するすべての Reliable Objects を作成します。 次に、Reliable Objects はバックアップ フォルダーのチェックポイントから復元するように指示されます。 最後に、Reliable State Manager はバックアップ フォルダー内のログ レコードからそれ自体の状態を復元し、復元を実行します。 復元プロセスの一環として、バックアップ フォルダーにコミット ログ レコードがある "開始ポイント" から始まる操作が Reliable Objects に対して再生されます。 この手順により、復元したステートに一貫性が与えられます。

## <a name="next-steps"></a>次のステップ
  - [Reliable Collection](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services の概要](service-fabric-reliable-services-quick-start.md)
  - [Reliable Services の通知](service-fabric-reliable-services-notifications.md)
  - [Reliable Services の構成](service-fabric-reliable-services-configuration.md)
  - [Reliable Collection の開発者向けリファレンス](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Azure Service Fabric での定期的なバックアップと復元](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
