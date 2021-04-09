---
title: Azure Blob Storage での NFS 3.0 のパフォーマンスに関する考慮事項 (プレビュー) | Microsoft Docs
description: この記事の推奨事項を使用して、ネットワーク ファイル システム (NFS) 3.0 のストレージ要求のパフォーマンスを最適化します。
author: normesta
ms.subservice: blobs
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: normesta
ms.reviewer: yzheng
ms.custom: references_regions
ms.openlocfilehash: 3b596d14ea770bfcd7560903a234d2ab77b66201
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102614328"
---
# <a name="network-file-system-nfs-30-performance-considerations-in-azure-blob-storage-preview"></a>Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 のパフォーマンスに関する考慮事項 (プレビュー)

Blob Storage では、ネットワーク ファイル システム (NFS) 3.0 プロトコルがサポートされるようになりました。 この記事には、ストレージ要求のパフォーマンスを最適化するのに役立つ推奨事項が含まれています。 Azure Blob Storage での NFS 3.0 のサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](network-file-system-protocol-support.md)」を参照してください。

> [!NOTE]
> Azure Blob Storage での NFS 3.0 プロトコルのサポートはパブリック プレビューの段階にあります。 次のリージョンでは、Standard パフォーマンス レベルを持つ GPV2 ストレージ アカウントがサポートされています。オーストラリア東部、韓国中部、米国中南部。 プレビューでは、すべてのパブリック リージョンで、Premium パフォーマンス レベルを持つブロック BLOB もサポートされています。

## <a name="add-clients-to-increase-throughput"></a>スループット向上のためにクライアントを追加する 

Azure Blob Storage は、ストレージ アカウントのエグレスとイングレスの上限に達するまで、直線的にスケーリングします。 したがって、アプリケーションで使用するクライアントが多いほど、より高いスループットを実現できます。  ストレージ アカウントのエグレスとイングレスの制限については、「[Standard Storage アカウントのスケーラビリティとパフォーマンスのターゲット](../common/scalability-targets-standard-account.md)」を参照してください。

次のグラフは、クライアントを追加すると帯域幅がどのように増加するかを示したものです。 このグラフのクライアントは仮想マシン (VM) であり、アカウントでは Standard パフォーマンス レベルが使用されています。 

> [!div class="mx-imgBorder"]
> ![Standard パフォーマンス](./media/network-file-system-protocol-support-performance/standard-performance-tier.png)

次のグラフは、Premium パフォーマンス レベルを使用するアカウントに同じ効果を適用した場合を示しています。

> [!div class="mx-imgBorder"]
> ![Premium パフォーマンス](./media/network-file-system-protocol-support-performance/premium-performance-tier.png)

## <a name="use-premium-performance-tier-for-small-scale-applications"></a>小規模なアプリケーションに Premium パフォーマンス レベルを使用する

クライアントを追加することにより、すべてのアプリケーションをスケールアップできるわけではありません。 そのようなアプリケーションの場合は、[Azure Premium ブロック BLOB ストレージ アカウント](storage-blob-create-account-block-blob.md)を使用することで、低遅延と高トランザクション レートが一貫して提供されます。 Premium ブロック BLOB ストレージ アカウントは、より少ないスレッドとクライアントで最大帯域幅に到達できます。 たとえば、クライアントが 1 つの場合、Standard パフォーマンスの汎用 v2 ストレージ アカウントで使用される同じセットアップと比較して、Premium ブロック BLOB ストレージ アカウントでは **2.3 倍** の帯域幅を実現できます。 

次のグラフの各棒は、Premium と Standard のパフォーマンス ストレージ アカウントで実現される帯域幅の違いを示しています。 クライアントの数が増えるにつれて、その違いは小さくなります。  

> [!div class="mx-imgBorder"]
> ![相対パフォーマンス](./media/network-file-system-protocol-support-performance/relative-performance.png)

## <a name="avoid-frequent-overwrites-on-date"></a>日付が頻繁に上書きされないようにする

新規書き込み操作より、上書き操作の方が完了するまでに時間がかかります。 これは、NFS の上書き操作 (特に、部分的なインプレース ファイル編集) が、読み取り、変更、書き込み操作といういくつかの基になる BLOB 操作を組み合わせたものであるためです。 そのため、頻繁にインプレース編集が必要なアプリケーションは、NFS が有効になっている BLOB ストレージ アカウントには適していません。 

## <a name="other-best-practice-recommendations"></a>その他のベスト プラクティスの推奨事項 

- 十分なネットワーク帯域幅を備えた VM を使用します。

- ワークロードで許容される場合は、複数のマウント ポイントを使用します。

- できるだけ多くのスレッドを使用します。

- 大きなブロック サイズを使用します。

- ストレージ アカウントと同じリージョンにあるクライアントからストレージ要求を行います。 これにより、ネットワークの待機時間が短縮されます。

## <a name="next-steps"></a>次のステップ

- Azure Blob Storage での NFS 3.0 のサポートの詳細については、「[Azure Blob Storage でのネットワーク ファイル システム (NFS) 3.0 プロトコルのサポート (プレビュー)](network-file-system-protocol-support.md)」を参照してください。

- 開始するには、「[ネットワーク ファイル システム (NFS) 3.0 プロトコル (プレビュー) を使用して Blob Storage をマウントする](network-file-system-protocol-support-how-to.md)」を参照してください。
