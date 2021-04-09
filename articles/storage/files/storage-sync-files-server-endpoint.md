---
title: Azure File Sync サーバー エンドポイントの追加/削除 | Microsoft Docs
description: Azure File Sync でサーバー エンドポイントを追加または削除する方法について説明します。サーバー エンドポイントは、サーバー ボリュームのフォルダーなど、登録済みサーバー上の特定の場所です。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013940"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Azure File Sync サーバー エンドポイントの追加/削除
Azure ファイル同期を使用すると、オンプレミスのファイル サーバーの柔軟性、パフォーマンス、互換性を損なわずに Azure Files で組織のファイル共有を一元化できます。 これは、Windows Server を Azure ファイル共有のクイック キャッシュに変換することで行います。 Windows Server で使用可能な任意のプロトコル (SMB、NFS、FTPS など) を使用してデータにローカル アクセスすることができ、世界中に必要な数だけキャッシュを持つことができます。

"*サーバー エンドポイント*" は、"*登録済みサーバー*" 上の特定の場所 (サーバー ボリュームのフォルダーやボリュームのルートなど) を表します。 名前空間が重複していない場合、各エンドポイントは一意の同期グループに同期していれば、同じボリューム上に複数のサーバー エンドポイントが存在する可能性があります (たとえば、F:\sync1 と F:\sync2 など)。 各サーバー エンドポイントについて、クラウドの階層化ポリシーを個別に構成することができます。 既存の一連のファイルを含むサーバーの場所をサーバー エンドポイントとして同期グループに追加すると、それらのファイルは、同期グループの他のエンドポイント上に既にある他のファイルと統合されます。

Azure File Sync をエンドツーエンドでデプロイする方法の詳細については、[Azure File Sync をデプロイする方法](storage-sync-files-deployment-guide.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件
サーバー エンドポイントを作成するには、まず次の条件を満たしていることを確認する必要があります。 
- サーバーに Azure ファイル同期エージェントがインストールされ、登録されていること。 Azure File Sync エージェントをインストールする手順については、[Azure File Sync でのサーバーの登録/登録解除](storage-sync-files-server-registration.md)に関するページを参照してください。 
- ストレージ同期サービスがデプロイされていること。 ストレージ同期サービスをデプロイする方法の詳細については、[Azure File Sync をデプロイする方法](storage-sync-files-deployment-guide.md)に関するページを参照してください。 
- 同期グループがデプロイされていること。 [同期グループの作成方法](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)に関するセクションをご覧ください。
- サーバーがインターネットに接続され、Azure にアクセスできること。 サーバーと Microsoft サービスとの間で行われるすべての通信にポート 443 を使用します。

## <a name="add-a-server-endpoint"></a>サーバー エンドポイントを追加する
サーバー エンドポイントを追加するには、目的の同期グループに移動し、[サーバー エンドポイントの追加] を選択します。

![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

**[サーバー エンドポイントの追加]** では、次の情報を指定する必要があります。

- **登録済みサーバー**: サーバー エンドポイントを作成するサーバーまたはクラスターの名前。
- **パス**: 同期グループの一部として同期する Windows Server 上のパス。
- **クラウドの階層化**: クラウドの階層化を有効または無効にするスイッチ。 クラウドの階層化を有効にすると、ファイルが Azure ファイル共有に "*階層化*" されます。 これにより、オンプレミスのファイル共有は、データセットの完全なコピーではなく、キャッシュに変換されるので、サーバーでの容量効率の管理に役立ちます。
- **ボリュームの空き領域**: サーバー エンドポイントが配置されているボリュームに確保する空き領域のサイズ。 たとえば、単一のサーバー エンドポイントで [ボリュームの空き領域] をボリュームの 50% に設定すると、データの約半量が Azure Files に階層化されます。 クラウドの階層化が有効かどうかにかかわらず、Azure ファイル共有は、データの完全なコピーを常に同期グループ内に保持します。

**[作成]** を選択してサーバー エンドポイントを追加します。 これで、同期グループの名前空間内のファイルは同期状態が維持されるようになります。 

## <a name="remove-a-server-endpoint"></a>サーバー エンドポイントを削除する
特定のサーバー エンドポイントで Azure File Sync の使用を中止する場合は、サーバー エンドポイントを削除できます。 

> [!Warning]  
> Microsoft のエンジニアによってはっきりと指示された場合を除き、サーバー エンドポイントを削除してから再作成することにより、同期、クラウドの階層化、または Azure File Sync のその他の側面に関する問題のトラブルシューティングを試みないでください。 サーバー エンドポイントの削除は破壊的な操作であり、サーバー エンドポイントが再作成された後で、サーバー エンドポイント内の階層化されたファイルは Azure ファイル共有上の場所に "再接続" されず、同期エラーが発生します。 また、サーバー エンドポイントの名前空間の外部に存在する階層化されたファイルは完全に失われる可能性があることにも注意してください。 クラウドの階層化が有効にされていなくても、階層化されたファイルがサーバー エンドポイント内に存在することがあります。

サーバー エンドポイントを削除する前に、すべての階層化されたファイルを確実に呼び戻すには、サーバー エンドポイントでクラウドの階層化を無効にした後、次の PowerShell コマンドレットを実行して、サーバー エンドポイントの名前空間内にあるすべての階層化されたファイルを呼び戻します。

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
`-Order CloudTieringPolicy` を指定すると、最後に変更されたファイルから先に呼び戻されます。
その他のお勧めの省略可能で便利なパラメーターを次に示します。
* `-ThreadCount` では、並行して呼び戻すことができるファイルの数を指定します。
* `-PerFileRetryCount` では、現在ブロックされているファイルの呼び戻しを試行する頻度を指定します。
* `-PerFileRetryDelaySeconds` では、再試行から呼び戻しまでの時間を秒単位で指定します。また、常に前のパラメーターと組み合わせて使用する必要があります。

> [!Note]  
> サーバーをホストするローカル ボリュームに、階層化されたデータをすべて回収できる空き領域がない場合、`Invoke-StorageSyncFileRecall` コマンドレットは失敗します。  

サーバー エンドポイントを削除するには、次の手順に従います。

1. サーバーが登録されているストレージ同期サービスに移動します。
2. 目的の同期グループに移動します。
3. ストレージ同期サービスの同期グループ内にある目的のサーバー エンドポイントを削除します。 これは、[同期グループ] ウィンドウの関連するサーバー エンドポイントを右クリックして実行できます。

    ![同期グループからサーバー エンドポイントを削除する](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>次のステップ
- [Azure File Sync でのサーバーの登録/登録解除](storage-sync-files-server-registration.md)
- [Azure File Sync のデプロイの計画](storage-sync-files-planning.md)
- [Azure File Sync の監視](storage-sync-files-monitoring.md)
