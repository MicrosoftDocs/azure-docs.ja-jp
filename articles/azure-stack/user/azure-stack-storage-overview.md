---
title: "Azure Stack Storage の概要"
description: "Azure Stack Storage について"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8777aa486a627cf8b2d8ba443e115638354d10da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-storage"></a>Azure Stack Storage の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

## <a name="overview"></a>概要
Azure Stack Storage は、Azure Storage サービスと一貫性がある BLOB、テーブル、およびキューを含むクラウド ストレージ サービスのセットです。

## <a name="azure-stack-storage-services"></a>Azure Stack Storage サービス
Azure Stack Storage 次の 3 つのサービスを提供します。

* **Blob Storage** 

    Blob Storage は、非構造化オブジェクト データを格納します。 ドキュメント、メディア ファイル、アプリケーション インストーラーなど、任意の種類のテキスト データやバイナリ データを BLOB として保存できます。
* **テーブル ストレージ** 

    テーブル ストレージには、構造型データが格納されます。 Table Storage は、NoSQL キー属性データ ストアであるため、開発が迅速化され、大量のデータにすばやくアクセスできます。
* **Queue Storage** 

    キュー ストレージは、ワークフロー処理およびクラウド サービスのコンポーネント間通信のための、信頼性の高いメッセージング機能を提供します。

Azure Stack Storage アカウントは、Azure Stack Storage のサービスにアクセスできる安全なアカウントです。 ストレージ アカウントは、ストレージ リソースに対して一意の名前空間を提供します。 以下の図は、ストレージ アカウントにおける Azure Stack Storage のリソース間の関係を示したものです。

![Azure Stack Storage の概要](media/azure-stack-storage-overview/AzureStackStorageOverview.png)


### <a name="blob-storage"></a>BLOB ストレージ

大量の非構造化オブジェクト データをクラウドに保存する場合は、Blob Storage を使用すると効率の高いスケーラブルなソリューションが実現します。 Blob Storage には、次のようなコンテンツを格納できます。

* ドキュメント
* 写真、ビデオ、音楽、ブログなどのソーシャル データ
* ファイル、コンピューター、データベース、およびデバイスのバックアップ データ
* Web アプリケーションのイメージとテキスト
* クラウド アプリケーションの構成データ
* ログやその他の大きなデータセットなどのビッグ データ

すべての BLOB は、コンテナーに編成されます。 コンテナーを使用すると、オブジェクトのグループにセキュリティ ポリシーを便利に割り当てることができます。 ストレージ アカウントの容量の上限を超えない限り、ストレージ アカウントには任意の数のコンテナーを含めることができ、コンテナーには任意の数の BLOB を含めることができます。

BLOB ストレージには、3 つの種類の BLOB が用意されています。 
* **ブロック BLOB** 

    ブロック BLOB はストリーミングとクラウド オブジェクトの格納に最適化されているので、ドキュメント、メディア ファイル、バックアップなどの格納に適しています。
* **追加 BLOB** 

    追加 BLOB はブロック BLOB に似ていますが、追加操作用に最適化されています。 追加 BLOB は、新しいブロックを最後に追加することによってのみ更新できます。 追加 BLOB は、新しいデータが BLOB の最後のみに書き込まれる必要がある、ログ記録のようなシナリオに適しています。
* **ページ BLOB** 

    ページ BLOB は、IaaS のディスクとして使用でき、ランダムな書き込みをサポートするように最適化され、最大 1 TB (テラバイト) までサイズを拡大できます。 Azure Stack 仮想マシンに設置された IaaS ディスクは、ページ BLOB として格納される VHD です。


### <a name="table-storage"></a>テーブル ストレージ
最新のアプリケーションの多くは、前の世代のソフトウェアよりも、拡張性と柔軟性に優れたデータ ストアを必要とします。 Table Storage は、高度な可用性と拡張性を備え、アプリケーションを需要に応じて自動的に拡張できます。 Table Storage は、Microsoft の NoSQL のキーまたは属性ストアですが、従来のリレーショナル データベースと異なり、スキーマなしの設計です。 スキーマなしのデータ ストアでは、アプリケーションの進化のニーズに合わせてデータを容易に修正できます。 Table Storage は使いやすいため、開発者はアプリケーションを迅速に作成できます。

テーブル ストレージは、キー属性ストアであるため、テーブル内のすべての値に型指定されたプロパティ名が付いて保存されます。 このプロパティ名は、フィルタリングや、選択条件の指定に使用できます。 1 つのエンティティは、一連のプロパティとその値で構成されます。 Table Storage はスキーマがないため、同じテーブル内の 2 つのエンティティが異なるコレクションのプロパティを持つことができ、それらのプロパティに異なる型を使用できます。

Table Storage を使用すると、Web アプリケーションのユーザー データ、アドレス帳、デバイス情報、およびサービスに必要なその他の種類のメタデータなど、柔軟なデータセットを保存できます。 今日のインターネットベースのアプリケーションでは、Table Storage のような NoSQL データベースが、従来のリレーショナル データベースの代わりに広く使用されています。

ストレージ アカウントの容量の上限を超えない限り、ストレージ アカウントには任意の数のテーブルを含めることができ、テーブルには任意の数のエンティティを保存できます。

### <a name="queue-storage"></a>Queue Storage
拡張性を重視してアプリケーションを設計する場合、通常、アプリケーション コンポーネントを個別に拡張できるように分離します。 Queue Storage では、アプリケーション コンポーネントがクラウド、デスクトップ、オンプレミスのサーバー、モバイル デバイスのいずれで実行されている場合でも、信頼性の高いメッセージング ソリューションによって、アプリケーション コンポーネント間の非同期通信が実行されます。 Queue Storage ではまた、非同期タスクの管理とプロセス ワークフローの構築もサポートします。

ストレージ アカウントの容量の上限を超えない限り、ストレージ アカウントには任意の数のキューを含めることができ、キューには任意の数のメッセージを保存できます。 1 つのメッセージは、最大 64 KB のサイズにすることができます。

## <a name="next-steps"></a>次のステップ
* [Azure 互換ストレージ: 違いと考慮事項](azure-stack-acs-differences.md)

* Azure Storage の詳細については、「[Introduction to Microsoft Azure Storage](../../storage/common/storage-introduction.md)」(Microsoft Azure Storage の概要) を参照してください。

