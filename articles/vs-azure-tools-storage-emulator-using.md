---
title: Visual Studio を使用したストレージ エミュレーターの構成と使用 | Microsoft Docs
description: Visual Studio を使用したストレージ エミュレーターの構成と使用
services: visual-studio-online
author: ghogen
manager: jillfra
assetId: c8e7996f-6027-4762-806e-614b93131867
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 8/17/2017
ms.author: ghogen
ms.openlocfilehash: 08e3f5d5bb32d15b9d8d164c898d2b8d7a90108c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2019
ms.locfileid: "71969713"
---
# <a name="configuring-and-using-the-storage-emulator-with-visual-studio"></a>Visual Studio を使用したストレージ エミュレーターの構成と使用
[!INCLUDE [storage-try-azure-tools](../includes/storage-try-azure-tools.md)]

## <a name="overview"></a>概要
Azure SDK 開発環境には、ローカル開発コンピューター上の Azure で使用可能な BLOB サービス、キュー サービス、テーブル サービスをシミュレートするユーティリティ、ストレージ エミュレーターが付属しています。 Azure ストレージ サービスを使用するクラウド サービスを構築している場合、またはストレージ サービスを呼び出す外部アプリケーションを作成している場合、ストレージ エミュレーターに対してコードをローカルでテストできます。 Azure Tools for Microsoft Visual Studio によって、ストレージ エミュレーターの管理が Visual Studio に統合されます。 Azure Tools は、初回使用時にストレージ エミュレーター データベースを初期化します。Visual Studio からコードが実行またはデバッグされるとストレージ エミュレーター サービスを開始し、Azure ストレージ エクスプローラーを介して、ストレージ エミュレーター データへの読み取り専用アクセスを提供します。

システム要件やカスタム構成の手順など、ストレージ エミュレーターについて詳しくは、「[開発とテストのための Azure のストレージ エミュレーター使用](storage/common/storage-use-emulator.md)」をご覧ください。

> [!NOTE]
> ストレージ エミュレーター シミュレーションと Azure ストレージ サービスの間には、機能上の違いがいくつかあります。 具体的な違いについて詳しくは、Azure SDK ドキュメントの「[ストレージ エミュレーターと Azure ストレージ サービスとの違い](storage/common/storage-use-emulator.md)」をご覧ください。
> 
> 

## <a name="configuring-a-connection-string-for-the-storage-emulator"></a>ストレージ エミュレーターの接続文字列の構成
ロール内のコードからストレージ エミュレーターにアクセスするには、ストレージ エミュレーターを接続先とする接続文字列 (後で Azure ストレージ アカウントを接続先とするように変更可能) を構成します。 接続文字列とは、ストレージ アカウントに接続する際、ロールから実行時に読み取ることができる構成設定です。 接続文字列の作成方法の詳細については、「[Azure Storage の接続文字列を構成する](/azure/storage/common/storage-configure-connection-string)」を参照してください。

> [!NOTE]
> **DevelopmentStorageAccount** プロパティを使用して、コードからストレージ エミュレーター アカウントへの参照を返すことができます。 このアプローチはコードからストレージ エミュレーターにアクセスする場合は問題ありませんが、アプリケーションを Azure に発行する計画がある場合は、発行する前に、Azure ストレージ アカウントにアクセスするための接続文字列を作成し、この接続文字列を使用するようにコードを変更する必要があります。 ストレージ エミュレーター アカウントと Azure ストレージ アカウントとを頻繁に切り替える場合は、接続文字列によってこの作業が簡略化されます。
> 
> 

## <a name="initializing-and-running-the-storage-emulator"></a>ストレージ エミュレーターの初期化と実行
Visual Studio でサービスを実行またはデバッグしたときにストレージ エミュレーターが自動的に起動するように指定することができます。 ソリューション エクスプローラーで、**Azure** プロジェクトのショートカット メニューを開き、 **[プロパティ]** をクリックします。 **[開発]** タブの **[Azure ストレージ エミュレーターの起動]** ボックスの一覧で、 **[True]** を選択します (まだそのように設定されていない場合)。

Visual Studio で初めてサービスを実行またはデバッグすると、ストレージ エミュレーターは初期化プロセスを起動します。 このプロセスによって、ストレージ エミュレーター用のローカル ポートが予約され、ストレージ エミュレーター データベースが作成されます。 完了したら、ストレージ エミュレーター データベースを削除しない限り、このプロセスを再度実行する必要はありません。

> [!NOTE]
> Azure Tools 2012 年 6 月リリース以降では、ストレージ エミュレーターは既定では SQL Express LocalDB で実行されます。 それより前にリリースされた Azure Tools では、ストレージ エミュレーターが SQL Express 2005 または SQL Express 2008 の既定のインスタンスに対して実行されます。この場合 Azure SDK をインストールするには、あらかじめ SQL Express をインストールしておく必要があります。 ストレージ エミュレーターは、SQL Express の名前付きインスタンスのほか、Microsoft SQL Server の名前付きインスタンスまたは既定のインスタンスに対しても実行できます。 既定のインスタンス以外のインスタンスに対して実行されるようにストレージ エミュレーターを構成する必要がある場合は、「[開発とテストのための Azure のストレージ エミュレーター使用](storage/common/storage-use-emulator.md)」をご覧ください。
> 
> 

ストレージ エミュレーターには、ローカル ストレージ サービスの状態の表示や、そのサービスの開始、停止、リセットを行うためのユーザー インターフェイスが用意されています。 ストレージ エミュレーター サービスが開始されたら、Windows タスク バーにある [Microsoft Azure エミュレーター] の通知領域アイコンを右クリックすることで、ユーザー インターフェイスを表示したり、サービスを開始または停止したりできます。

## <a name="viewing-storage-emulator-data-in-server-explorer"></a>サーバー エクスプローラーでのストレージ エミュレーター データの表示
ストレージ エミュレーターをはじめとするストレージ アカウント内の BLOB とテーブルのデータについては、サーバー エクスプローラーの Azure の [ストレージ] ノードを使用してデータを表示したり設定を変更したりすることができます。 詳細については「[ストレージ エクスプローラー を使用した Azure Blob Storage リソースの管理](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)」を参照してください。

