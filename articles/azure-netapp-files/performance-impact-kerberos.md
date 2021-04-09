---
title: Azure NetApp Files NFSv 4.1 ボリュームでの Kerberos のパフォーマンスへの影響 | Microsoft Docs
description: Azure NetApp Files NFSv 4.1 ボリュームでの Kerberos に関する使用可能なセキュリティ オプション、テストされたパフォーマンス ベクトル、および予想されるパフォーマンスへの影響について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: b-juche
ms.openlocfilehash: e9054f11c8f55e9fe00266840a9f6e257f14e659
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744041"
---
# <a name="performance-impact-of-kerberos-on-azure-netapp-files-nfsv41-volumes"></a>Azure NetApp Files NFSv 4.1 ボリュームでの Kerberos のパフォーマンスへの影響

Azure NetApp Files では、AES-256 暗号化を使用した [Kerberos モード (krb5、krb5i、krb5p) の NFS クライアント暗号化](configure-kerberos-encryption.md)がサポートされています。 この記事では、NFSv4.1 ボリュームでの Kerberos によるパフォーマンスへの影響について説明します。 

## <a name="available-security-options"></a>利用可能なセキュリティ オプション 

NFSv4.1 ボリュームで現在使用できるセキュリティ オプションは次のとおりです。 

* **sec=sys** を指定すると、NFS 操作を認証するために AUTH_SYS を使用する ことで、ローカルの UNIX UID および GID が使用されます。
* **sec=krb5** を指定すると、ユーザーを認証するために、ローカルの UNIX UID および GID ではなく、Kerberos V5 が使用されます。
* **sec=krb5i** を指定すると、ユーザー認証に Kerberos V5 が使用され、データの改ざんを防ぐためにセキュリティで保護されたチェックサムを使用して NFS 操作の整合性チェックが実行されます。
* **sec=krb5p** を指定すると、ユーザー認証と整合性チェックに Kerberos V5 が使用されます。 トラフィック スニッフィングを防ぐために、NFS トラフィックが暗号化されます。 このオプションは最も安全な設定ですが、パフォーマンスのオーバーヘッドが最も高くなります。

## <a name="performance-vectors-tested"></a>テスト済みのパフォーマンス ベクトル

ここでは、さまざまな `sec=*` オプションの単一のクライアント側パフォーマンスに対する影響について説明します。

* パフォーマンスに対する影響は、低いコンカレンシー (低負荷) と高いコンカレンシー (I/O とスループットの上限) の 2 つのレベルでテストされました。  
* 3 種類のワークロードがテストされました。  
    * 小規模な操作のランダム読み取り/書き込み (FIO を使用)
    * 大規模な操作のシーケンシャル読み取り/書き込み (FIO を使用)
    * git などのアプリケーションによって生成されるメタデータの負荷が高いワークロード

## <a name="expected-performance-impact"></a>予想されるパフォーマンスに対する影響 

焦点となる領域が 2 つがあります。 軽い負荷と上限です。 次の一覧では、パフォーマンスに対する影響について、セキュリティ設定別とシナリオ別に説明します。 比較はすべて `sec=sys` セキュリティ パラメーターに対して行われています。 1 つのクライアントを使用して、1 つのボリュームでテストが実行されました。 

krb5 のパフォーマンスに対する影響:

* 低いコンカレンシー (r/w):
    * シーケンシャルの待機時間が 0.3 ミリ秒増えました。
    * ランダム I/O 待機時間が 0.2 ミリ秒増えました。
    * メタデータの I/O 待機時間が 0.2 ミリ秒増えました。
* 高いコンカレンシー (r/w): 
    * 最大シーケンシャル スループットは、krb5 によって影響を受けませんでした。
    * 純粋な読み取りワークロードでは最大ランダム I/O が 30% 減少し、ワークロードが純粋な書き込みに移行すると全体的な影響はゼロに減少しました。 
    * 最大メタデータ ワークロードが 30% 減少しました。

krb5i のパフォーマンスに対する影響: 

* 低いコンカレンシー (r/w):
    * シーケンシャルの待機時間が 0.5 ミリ秒増えました。
    * ランダム I/O 待機時間が 0.2 ミリ秒増えました。
    * メタデータの I/O 待機時間が 0.2 ミリ秒増えました。
* 高いコンカレンシー (r/w): 
    * 最大シーケンシャル スループットは、ワークロードの組み合わせに関係なく、全体で 70% 減少しました。
    * 純粋な読み取りワークロードでは最大ランダム I/O が 50% 減少し、ワークロードが純粋な書き込みに移行すると全体的な影響は 25% に低下しました。 
    * 最大メタデータ ワークロードが 30% 減少しました。

krb5p のパフォーマンに対する影響:

* 低いコンカレンシー (r/w):
    * シーケンシャルの待機時間が 0.8 ミリ秒増えました。
    * ランダム I/O 待機時間が 0.2 ミリ秒増えました。
    * メタデータの I/O 待機時間が 0.2 ミリ秒増えました。
* 高いコンカレンシー (r/w): 
    * 最大シーケンシャル スループットは、ワークロードの組み合わせに関係なく、全体で 85% 減少しました。 
    * 純粋な読み取りワークロードでは最大ランダム I/O が 65% 減少し、ワークロードが純粋な書き込みに移行すると全体的な影響は 43% に低下しました。 
    * 最大メタデータ ワークロードが 30% 減少しました。

## <a name="next-steps"></a>次のステップ  

* [Azure NetApp Files の NFSv4.1 の Kerberos 暗号化を構成する](configure-kerberos-encryption.md) 
