---
title: Azure Security Center に対する今後の重要な変更
description: 知っておく必要があり、計画する必要がある Azure Security Center の今後の変更
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: memildin
ms.openlocfilehash: d5de16c8156762a229d6c707080bc197dc206a7c
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475592"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>Azure Security Center に対する今後の重要な変更

> [!IMPORTANT]
> このページの情報は、販売を開始する前に大幅に変更される可能性があるプレリリース製品または機能に関連しています。 Microsoft は、ここで提供される情報に対して、明示または黙示を問わず、一切の確約または保証を行わないものとします。

このページでは、Security Center に対して計画されている変更について説明します。 ここでは、セキュリティ スコアやワークフローなどに影響する可能性がある、製品に対して計画されている変更について説明します。

最新のリリース ノートをお探しの場合は、「[Azure Security Center の最新情報](release-notes.md)」をご覧ください。


## <a name="planned-changes"></a>計画されている変更

- [Kubernetes ワークロード保護の推奨事項が間もなく一般提供 (GA) リリースへ](#kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga)
- ["システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化](#two-recommendations-from-apply-system-updates-security-control-being-deprecated)
- [SQL データ分類の推奨事項を改善](#enhancements-to-sql-data-classification-recommendation)


### <a name="kubernetes-workload-protection-recommendations-will-soon-be-released-for-general-availability-ga"></a>Kubernetes ワークロード保護の推奨事項が間もなく一般提供 (GA) リリースへ

**変更予定日:** 2021 年 2 月

「[Kubernetes ワークロードを保護する](kubernetes-workload-protections.md)」で説明されている Kubernetes ワークロード保護の推奨事項は、現在プレビュー段階です。 プレビュー段階の推奨事項の観点からリソースが異常と見なされることはなく、これらの推奨事項はセキュア スコアの計算にも含まれません。

これらの推奨事項は間もなく一般提供 (GA) リリースとなり、"*今後*"、スコアの計算に含まれるようになります。 その修復がまだ済んでいない場合、これが原因でセキュア スコアに若干の影響が生じる可能性があります。

それらは可能な限り修復してください (その方法については、「[Azure Security Center の修復レコメンデーション](security-center-remediate-recommendations.md)」を参照してください)。

Kubernetes ワークロード保護の推奨事項は次のとおりです。

- Kubernetes 用の Azure Policy アドオンをクラスターにインストールして有効にする必要がある
- コンテナーの CPU とメモリの制限を強制する必要がある
- 特権コンテナーの使用を避ける
- コンテナーで不変 (読み取り専用) のルート ファイル システムを適用する必要がある
- 特権エスカレーションを含むコンテナーは避ける必要がある
- コンテナーをルート ユーザーとして実行しない
- 機密性の高いホストの名前空間を共有するコンテナーは避ける必要がある
- コンテナーで最小限の特権を持つ Linux 機能を適用する必要がある
- ポッド HostPath ボリューム マウントの使用は既知のリストに制限する必要がある
- コンテナーは許可されたポートでのみリッスンする必要がある
- サービスは許可されたポートでのみリッスンする必要がある
- ホスト ネットワークとポートの使用を制限する必要がある
- コンテナーの AppArmor プロファイルのオーバーライドまたは無効化を制限する必要がある
- コンテナー イメージは信頼されたレジストリからのみデプロイする必要がある             

これらの推奨事項の詳細については、「[Kubernetes ワークロードを保護する](kubernetes-workload-protections.md)」を参照してください。

### <a name="two-recommendations-from-apply-system-updates-security-control-being-deprecated"></a>"システムの更新プログラムを適用する" セキュリティ コントロールの 2 つの推奨事項を非推奨化 

**変更予定日:** 2021 年 2 月

次の 2 つの推奨事項が 2021 年 2 月に非推奨化される予定です。

- **システムの更新プログラムを適用するには、マシンを再起動する必要があります**。 これが原因でセキュア スコアに若干の影響が生じる可能性があります。
- **お使いのマシンに監視エージェントをインストールする必要があります**。 この推奨事項は、オンプレミスのマシンにのみ関係します。また、そのロジックのいくつかは、別の推奨事項 (**お使いのマシンで Log Analytics エージェントの正常性の問題を解決する必要がある**) に移される予定です。 これが原因でセキュア スコアに若干の影響が生じる可能性があります。

連続エクスポートとワークフロー自動化の構成をチェックして、該当する推奨事項が含まれているかどうかを確認することをお勧めします。 また、ダッシュボードまたはそれらを使用する他の監視ツールを適宜更新する必要があります。

これらの推奨事項の詳細については、[セキュリティに関する推奨事項のリファレンス ページ](recommendations-reference.md)を参照してください。


### <a name="enhancements-to-sql-data-classification-recommendation"></a>SQL データ分類の推奨事項を改善

**変更予定日:** 2021 年第 2 四半期

"**データ分類の適用**" セキュリティ コントロールにおける現行バージョンの推奨事項 "**SQL データベースの機密データを分類する必要がある**" は、より Microsoft のデータ分類戦略に沿った新しいバージョンに置き換えられる予定です。 その結果、次のような影響が出ています。

- 今後、推奨事項はセキュア スコアに影響しません。
- 今後、セキュリティ コントロール ("データ分類の適用") はセキュア スコアに影響しません。
- 推奨事項の ID も変更されます (現在は b0df6f56-862d-4730-8597-38c0fd4ebd59)。



## <a name="next-steps"></a>次のステップ

製品に対する最近のすべての変更については、「[Azure Security Center の最新情報](release-notes.md)」を参照してください。
