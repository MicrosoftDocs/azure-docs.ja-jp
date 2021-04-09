---
title: Batch サービス API を使用して出力のデータを Azure Storage に保持する
description: Batch サービス API を使用して Batch タスクおよびジョブの出力データを Azure Storage に保持する方法について説明します。
ms.topic: how-to
ms.date: 07/30/2020
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 720c064c6b382bc62565c0828422181c761df8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88936930"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Batch サービス API を使用してタスクのデータを Azure Storage に保持する

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Batch サービス API は、仮想マシン構成のプールで実行されるタスクおよびジョブ マネージャー タスクについて、Azure Storage への出力データの保持をサポートします。 タスクを追加する時点で、タスクの出力の保存先として Azure Storage 内のコンテナーを指定できます。 そうすると、タスクが完了した時点で、Batch サービスによってすべての出力データがそのコンテナーに書き込まれます。

タスクの出力を保持するために Batch サービス API を使用することの利点は、タスクが実行されるアプリケーションに変更を加える必要がないところにあります。 代わりに、クライアント アプリケーションにいくつか変更を加えれば、タスクを作成する同じコード内からタスクの出力を保持できます。

> [!IMPORTANT]
> Batch サービス API を使用した Azure Storage へのタスク データの保持は、[2018 年 2 月 1 日](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md#1204)以前に作成されたプールでは機能しません。

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Batch サービス API を使用してタスク出力を保持するとよいケース

Azure Batch は、タスクの出力を保持するために複数の方法を提供しています。 次のようなシナリオは、Batch サービス API を使用するのが最適なケースです。

- タスクが実行されているアプリケーションを変更せずに、タスクの出力をクライアント アプリケーション内から保持するためのコードを記述する。
- 仮想マシンの構成で作成されたプール内の Batch タスクおよびジョブ マネージャー タスクからの出力を保持する。
- 任意の名前の Azure Storage コンテナーに出力を保持する。
- [Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)に準拠した名前の Azure Storage コンテナーに出力を保持する。

検討しているシナリオが上記のシナリオと異なる場合は、別のアプローチを考慮する必要があります。 たとえば、Batch サービス API は現時点で、タスクの実行中に Azure Storage に出力をストリーミングする機能をサポートしていません。 出力をストリーミングするには、.NET の場合に利用可能な Batch ファイル規則ライブラリの使用をご検討ください。 その他の言語の場合には、開発者が自分で独自のソリューションを実装する必要があります。 タスクの出力を保持するために利用できるその他のオプションの詳細については、「[ジョブやタスクからの出力を Azure Storage に保存する](batch-task-output.md)」を参照してください。

## <a name="create-a-container-in-azure-storage"></a>Azure Storage 内にコンテナーを作成する

タスクの出力を Azure Storage に保持するためには、出力ファイルの保存先となるコンテナーを作成する必要があります。 コンテナーの作成は、タスクを実行する前、可能であれば、ジョブを送信する前に行います。 コンテナーを作成するには、適切な Azure Storage クライアント ライブラリまたは SDK を使用します。 Azure Storage API の詳細については、[Azure Storage のドキュメント](../storage/index.yml)を参照してください。

たとえば、C# でアプリケーションを作成している場合は、[.NET 用の Azure Storage クライアント ライブラリ](https://www.nuget.org/packages/WindowsAzure.Storage/)を使用します。 次の例は、コンテナーを作成する方法を示しています。

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>コンテナーの Shared Access Signature を取得する

コンテナーを作成した後は、コンテナーに対する書き込みアクセス権を持つ Shared Access Signature (SAS) を取得します。 SAS は、コンテナーへの委任されたアクセスを提供します。 SAS は、指定した一式のアクセス許可を、指定した期間にわたって付与します。 Batch サービスでは、タスクの出力をコンテナーに書き込むために、書き込みアクセス許可を持つ SAS が必要になります。 SAS の詳細については、「[Azure Storage での Shared Access Signatures \(SAS\) の使用](../storage/common/storage-sas-overview.md)」を参照してください。

Azure Storage API を使用して SAS を取得すると、API から SAS トークン文字列が返されます。 このトークン文字列には、アクセス許可と SAS の有効期間を含む、SAS のすべてのパラメーターが含まれています。 SAS を使用して Azure Storage のコンテナーにアクセスするには、SAS トークン文字列をリソース URI に追加する必要があります。 このリソース URI と、追加された SAS トークンにより、Azure Storage への認証済みアクセスが提供されます。

次の例は、コンテナーの書き込み専用の SAS トークン文字列を取得し、その SAS をコンテナーの URI に追加する方法を示しています。

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>タスク出力用に出力ファイルを指定する

タスクに対して出力ファイルを指定するには、[OutputFile](/dotnet/api/microsoft.azure.batch.outputfile) オブジェクトのコレクションを作成し、タスクの作成時にそのコレクションを [CloudTask.OutputFiles](/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) プロパティに割り当てます。

次に示す C# コードの例では、`output.txt` という名前のファイルに乱数を書き込むタスクを作成しています。 この例では、`output.txt` をコンテナーに書き込むために出力ファイルを作成します。 また、出力ファイル、ファイル パターン `std*.txt` に一致するすべてのログ ファイル (_例_: `stdout.txt` や `stderr.txt` など) についても出力ファイルを作成します。 コンテナーの URL には、事前にコンテナーに対して作成した SAS が必要になります。 Batch サービスでは、その SAS を使用して、コンテナーへのアクセスを認証します。

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

> [!NOTE]
> Linux でこの例を使用する場合は、円記号をスラッシュに変更してください。

### <a name="specify-a-file-pattern-for-matching"></a>ファイル パターンの一致を指定する

出力ファイルを指定するとき、[OutputFile.FilePattern](/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) プロパティを使って、ファイル パターンの一致を指定できます。 このファイル パターンは、タスクによって作成される 0 個のファイル、1 個のファイル、一連のファイルと一致する可能性があります。

**FilePattern** プロパティでは、`*` (非再帰的に一致) や `**` (再帰的に一致) など、標準のファイル システム ワイルドカードがサポートされます。 たとえば、上記のコード サンプルでは、`std*.txt` と非再帰的に一致するファイル パターンを次のように指定しています。

`filePattern: @"..\std*.txt"`

1 つのファイルをアップロードするには、ワイルドカードなしでファイル パターンを指定します。 たとえば、上記のコード サンプルでは、`output.txt` と一致するファイル パターンを次のように指定しています。

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>アップロード条件を指定する

[OutputFileUploadOptions.UploadCondition](/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) プロパティにより、出力ファイルを条件付きでアップロードすることができます。 一般的なシナリオとして、タスクが成功した場合に特定の一連のファイルをアップロードし、タスクが失敗した場合にそれとは異なる一連のファイルをアップロードするというものがあります。 たとえば、タスクが失敗し、ゼロ以外の終了コードで終了した場合にのみ、詳細なログ ファイルをアップロードすることができます。 同様に、タスクが成功した場合にのみ、結果ファイルをアップロードすることができます。タスクが失敗した場合は、全部または一部の結果ファイルが存在しない可能性があるからです。

上記のコード サンプルでは、**UploadCondition** プロパティを **TaskCompletion** に設定しています。 この設定では、タスクの完了後に、終了コードの値にかかわらず、ファイルをアップロードすることが指定されます。

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

その他の設定については、[OutputFileUploadCondition](/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) 列挙型を参照してください。

### <a name="disambiguate-files-with-the-same-name"></a>同じ名前のファイルのあいまいさを解消する

1 つのジョブ内の複数のタスクから、同じ名前のファイルが生成されることがあります。 たとえば、`stdout.txt` と `stderr.txt` は、1 つのジョブ内で実行されるすべてのタスクで作成されます。 各タスクは独自のコンテキストで実行されるため、これらのファイルがノードのファイル システム上で競合することはありません。 ただし、複数のタスクから共有コンテナーにファイルをアップロードする場合は、同じ名前のファイルのあいまいさを解消する必要があります。

[OutputFileBlobContainerDestination.Path](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) プロパティでは、出力ファイル用の保存先 BLOB または仮想ディレクトリを指定します。 **Path** プロパティを使用して、BLOB または仮想ディレクトリの名前について、同じ名前の出力ファイルが Azure Storage 内で一意の名前になるように設定できます。 パスにタスク ID を使用する方法は、確実に一意の名前を作成でき、ファイルを容易に識別できるので、優れた方法です。

**FilePattern** プロパティをワイルドカード式に設定した場合は、そのパターンに一致するすべてのファイルが、**Path** プロパティで指定した仮想ディレクトリにアップロードされます。 たとえば、コンテナーが `mycontainer`、タスク ID が `mytask`、ファイル パターンが `..\std*.txt` の場合であれば、Azure Storage 内の出力ファイルへの絶対 URI は次のようになります。

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

**FilePattern** プロパティを 1 つのファイル名と一致するように設定した場合、つまりワイルドカード文字を含めない場合は、**Path** プロパティの値は BLOB の完全修飾名を指定します。 複数のタスクから生成される 1 つのファイルで名前の競合が予想される場合は、ファイル名の一部として仮想ディレクトリの名前を含めることにより、それらのファイルのあいまいさを解消します。 たとえば、**Path** プロパティを、タスク ID、区切り文字 (通常はスラッシュ)、ファイル名を含めた値に設定します。

`path: taskId + @"/output.txt"`

一連のタスクの出力ファイルへの絶対 URI は、次のようになります。

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Azure Storage 内の仮想ディレクトリの詳細については、「[コンテナー内の BLOB を一覧表示する](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)」を参照してください。

## <a name="diagnose-file-upload-errors"></a>ファイルのアップロード エラーを診断する

Azure Storage への出力ファイルのアップロードが失敗した場合、そのタスクは **Completed** 状態に移行し、[TaskExecutionInformation.FailureInformation](/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) プロパティが設定されます。 **FailureInformation** プロパティを確認すれば、どのようなエラーが発生したかを判別できます。 たとえば、次に示すのは、コンテナーが見つからない場合にファイルをアップロードしたときに発生するエラーです。

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

各ファイルのアップロードのたびに、Batch は、`fileuploadout.txt` と `fileuploaderr.txt` の 2 つのログ ファイルをコンピューティング ノードに書き出します。 特定のエラーの詳細について、これらのログ ファイルを確認することができます。 タスクそのものを実行できなかった場合など、ファイルのアップロードがまったく試行されなかった場合には、これらのログ ファイルは存在しません。

## <a name="diagnose-file-upload-performance"></a>ファイルのアップロード パフォーマンスを診断する

`fileuploadout.txt` ファイルには、アップロードの進行状況ログが記録されます。 このファイルを調べると、ファイルのアップロードにどれほど時間がかかったかについて、詳細を把握できます。 アップロードのパフォーマンスには、ノードのサイズ、アップロード時にノード上で実行されている他のアクティビティ、アップロード先のコンテナーが Batch プールと同じリージョンにあるかどうか、ストレージ アカウントに同時にアップロードしているノードの数など、多くの要因が影響を与えることにご注意ください。

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Batch サービス API を Batch ファイル規則の標準と併用する

Batch サービス API でタスクの出力を保持する場合、保存先コンテナーや BLOB の名前は、開発者が自由に付けることができます。 あるいは、[Batch ファイル規則の標準](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files)に従って名前を付けることもできます。 ファイル規則の標準では、特定の出力ファイルについて Azure Storage 内での保存先コンテナーと BLOB の名前を決める際に、ジョブとタスクの名前を基にします。 出力ファイルの名前付けにファイル規則の標準を使用した場合は、[Azure Portal](https://portal.azure.com) で出力ファイルを表示できます。

C# で開発している場合は、[.NET 用の Batch ファイル規則ライブラリ](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files)に組み込まれているメソッドを使用することができます。 このライブラリでは、適切に名前を付けたコンテナーと BLOB のパスが自動的に作成されます。 たとえば、ジョブ名に基づいてコンテナーの正しい名前を取得する API を呼び出すことができます。

```csharp
string containerName = job.OutputStorageContainerName();
```

[CloudJobExtensions.GetOutputStorageContainerUrl](/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) メソッドを使用すると、コンテナーへの書き込みに使用する Shared Access Signature (SAS) URL が返されます。 その後、この SAS を [OutputFileBlobContainerDestination](/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) コンストラクターに渡すことができます。

C# 以外の言語で開発している場合は、自分でファイル規則の標準を実装する必要があります。

## <a name="code-sample"></a>コード サンプル

[PersistOutputs](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs) サンプル プロジェクトは、GitHub にある [Azure Batch コード サンプル](https://github.com/Azure/azure-batch-samples)の 1 つです。 この Visual Studio ソリューションは、.NET 用の Batch クライアント ライブラリを使用して持続性のあるストレージにタスク出力を保持する方法を示しています。 サンプルを実行するには、次の手順に従います。

1. **Visual Studio 2019** でプロジェクトを開きます。
2. Microsoft.Azure.Batch.Samples.Common プロジェクトの **AccountSettings.settings** に、Batch と Storage の **アカウント資格情報** を追加します。
3. **ビルド** します (ただし実行はしないでください)。 NuGet パッケージの復元を求められた場合は、復元します。
4. Azure ポータルを使用して、 [アプリケーション パッケージ](batch-application-packages.md) を **PersistOutputsTask** としてアップロードします。 `PersistOutputsTask.exe` とそれに依存するアセンブリを .zip パッケージに含め、アプリケーション ID を "PersistOutputsTask"、アプリケーション パッケージのバージョンを "1.0" に設定します。
5. **PersistOutputs** プロジェクトを **開始** (実行) します。
6. サンプルの実行に使用する保持テクノロジを選択するように求められたら、「**2**」と入力して、タスク出力の保持に Batch サービス API を使用してサンプルを実行します。
7. 必要であれば、サンプルをもう一度実行し、「**3**」と入力して、Batch サービス API を使用して出力を保持するとともに、ファイル規則の標準に準拠して保存先のコンテナーと BLOB のパスに名前を付けます。

## <a name="next-steps"></a>次のステップ

- .NET 用のファイル規則ライブラリを使用してタスク出力を保持する方法について詳しくは、「[.NET 用の Batch ファイル規則ライブラリを使用した Azure Storage へのジョブおよびタスクのデータの保持](batch-task-output-file-conventions.md)」を参照してください。
- Azure Batch で出力データを保持するためのその他の方法については、「[ジョブやタスクからの出力を Azure Storage に保存する](batch-task-output.md)」を参照してください。
