---
title: Azure HPC キャッシュを作成する
description: Azure HPC Cache インスタンスを作成する方法
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: c3d14eaefaa1f317cb061273866ffee83747f12b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036839"
---
# <a name="configure-aggregated-namespace"></a>集約された名前空間を構成する
<!-- change link in GUI -->

Azure HPC Cache を使用すると、バックエンド ストレージ システムの細部が見えないようにする仮想名前空間を通じて、クライアントはさまざまなストレージ システムにアクセスすることができます。

ストレージ ターゲットを追加するときに、クライアント側のファイルパスを設定します。 クライアント マシンは、このファイルパスをマウントすることになります。 そのパスに関連付けられるストレージ ターゲットは変更できます。 たとえば、クライアント側の手順を変更することなく、ハードウェア ストレージ システムをクラウド ストレージに置き換えることができます。

## <a name="aggregated-namespace-example"></a>集約された名前空間の例

集約された名前空間を設計するにあたっては、クライアント マシンが必要な情報に手間なくアクセスし、また管理者やワークフロー エンジニアがパスを容易に識別できるようにしてください。

たとえば、Azure BLOB の格納データの処理に Azure HPC Cache インスタンスが使用されるシステムを考えてみます。 その分析には、オンプレミス データセンターに格納されたテンプレート ファイルが必要です。

テンプレート データはデータセンターに格納され、このジョブに必要な情報は次のサブディレクトリに格納されています。

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

データセンターのストレージ システムは、次のエクスポートを公開します。 

    /
    /goldline
    /goldline/templates

分析の対象となるデータは、[CLFSLoad ユーティリティ](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)を使用して、"sourcecollection" という名前の Azure Blob Storage コンテナーにコピーされています。

キャッシュ経由で簡単にアクセスできるようにするために、次の仮想名前空間パスを使用してストレージ ターゲットを作成することを検討します。

| バックエンド NFS ファイルパスまたは BLOB コンテナー | 仮想名前空間パス |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

NFS ソース パスは同じエクスポートのサブディレクトリであるため、同じストレージ ターゲットから複数の名前空間パスを定義する必要があります。 

| ストレージ ターゲットのホスト名  | NFS エクスポートのパス      | サブディレクトリのパス | 名前空間のパス    |
|--------------------------|----------------------|-------------------|-------------------|
| *<IP アドレスまたはホスト名>* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *<IP アドレスまたはホスト名>* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

クライアント アプリケーションはキャッシュをマウントして、集約された名前空間のファイルパス (/source、/templates/sku798、/templates/sku980) に容易にアクセスできます。

## <a name="next-steps"></a>次の手順

仮想ファイル システムの設定方法が決まったら、[ストレージ ターゲットを作成](hpc-cache-add-storage.md)して、バックエンド ストレージをクライアント側の仮想ファイルパスにマップします。
