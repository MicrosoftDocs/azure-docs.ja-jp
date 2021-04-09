---
title: Azure Resource Health の FAQ
description: Azure Resource Health の概要
ms.topic: conceptual
ms.date: 01/29/2019
ms.openlocfilehash: dc24688d8393b7a13e8c93d3f4d0171cce4c213c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570061"
---
# <a name="azure-resource-health-faq"></a>Azure Resource Health の FAQ
Azure Resource Health についてよく寄せられる質問の回答を説明します。

## <a name="what-is-azure-resource-health"></a>Azure Resource Health とは
Resource Health は、リソースに影響を及ぼす Azure の問題が発生した際の診断とサポート利用を支援します。 リソースの現在と過去の正常性に関する情報が表示され、問題を軽減するのに役立ちます。 Resource Health は、Azure のサービスの問題についてサポートが必要な場合にテクニカル サポートを提供します。  

## <a name="what-is-the-resource-health-intended-for"></a>Resource Health の目的は何ですか。
リソースの問題が検出されると、Resource Health を利用して根本的な原因を診断できます。 Azure サービスの問題について支援が必要な場合は、問題を軽減するためのサポートとテクニカル サポートを提供します。

## <a name="what-health-checks-are-performed-by-resource-health"></a>Resource Health ではどのような正常性チェックが実行されますか。
Resource Health では、[リソースの種類](resource-health-checks-resource-types.md)に基づいてさまざまなチェックを実行します。 これらのチェックは、次の 3 種類の問題に対して実装されています。 
- 計画外のイベント。予期しないホストの再起動など。
- 計画的なイベント。スケジュールされたホスト OS の更新プログラムなど。
- ユーザー アクションによってトリガーされるイベント。ユーザーによる仮想マシンの再起動など。

## <a name="what-does-each-of-the-health-status-mean"></a>正常性状態はそれぞれ何を意味しますか。
正常性状態には次の 3 種類があります。
- 使用可能:このリソースに影響を与える可能性のある既知の問題は、この Azure プラットフォームにはありません
- 使用不可:リソースに影響を与えている問題が Resource Health によって検出されました
- 不明:リソースの正常性に関する情報の受信が停止されているため、リソースの正常性を Resource Health で判断できません。 

## <a name="what-does-the-unknown-status-mean-is-something-wrong-with-my-resource"></a>不明な状態とはどういう状態ですか。 リソースに何か問題がありますか。
正常性状態が "不明" に設定されるのは、Resource Health で特定のリソースに関する情報の受信が停止されているときです。 この状態はリソースの状態を明確に示しているわけではありませんが、問題が発生している場合は、Azure の問題を示している可能性があります。

## <a name="how-can-i-get-help-for-a-resource-that-is-unavailable"></a>使用不可のリソースについてサポートを受けるにはどうすればよいですか。
[リソース正常性] ブレードからサポート リクエストを送信できます。 プラットフォームのイベントが原因でリソースが使用不可になっている場合は、リクエストの作成にマイクロソフトとのサポート契約は必要ありません。

## <a name="does-resource-health-differentiate-between-unavailability-caused-by-platform-problems-versus-something-i-did"></a>Resource Health では、プラットフォームの問題に起因する使用不可とユーザーの操作に起因する使用不可は区別されますか?
はい、リソースが使用不可になっている場合は、Resource Health で、以下のいずれかのカテゴリ内で根本原因が識別されます。 
-   ユーザーが開始したアクション
-   計画的なイベント 
-   計画外のイベント

ポータルでは、ユーザーが開始したアクションは青の通知アイコンを使用して表示されますが、計画的および計画外のイベントは赤色の警告アイコンを使用して表示されます。 詳細については、[Resource Health の概要](Resource-health-overview.md)に関するページを参照してください。  

## <a name="can-i-integrate-resource-health-with-my-monitoring-tools"></a>Resource Health をユーザー固有の監視ツールと統合できますか。
Resource Health は、アクティビティ ログ ベースのアラートを[サポートしています](resource-health-alert-arm-template-guide.md)。 アクティビティ ログ アラートでは、[アクション グループ](../azure-monitor/alerts/action-groups.md)を使用して、アラートがトリガーされたことをユーザーに通知します。 アクション グループでは、メール、SMS、Webhook、ITSM アクションなどのさまざまな通知チャネルがサポートされています。

## <a name="where-do-i-find-resource-health"></a>Resource Health はどこにありますか。
Azure Portal にログインした後、複数の方法で Resource Health にアクセスできます。
- 対象のリソースに移動します。 左側のナビゲーションで、 **[リソース正常性]** を選択します。
- [Azure Service Health] ブレードに移動します。  左側のナビゲーションで、 **[リソース正常性]** を選択します。

Resource Health API を使用してリソースの正常性に関する情報を取得することもできます。

## <a name="is-resource-health-available-for-all-resource-types"></a>Resource Health はすべてのリソースの種類で利用できますか。
Resource Health でサポートされている正常性チェックとリソースの種類の一覧は[こちら](resource-health-checks-resource-types.md)で確認できます。

## <a name="what-should-i-do-if-my-resource-is-showing-available-but-i-believe-it-is-not"></a>リソースは使用可能と表示されているが、そのように思えない場合はどうすればよいですか。
リソースの状態を確認するには、正常性状態のすぐ下にある **[Report incorrect health status (不適切な正常性状態を報告)]** をクリックします。 レポートを送信する前に、現在の正常性状態が正しくないと思われる理由の詳細を、必要に応じて入力することができます。

## <a name="is-resource-health-available-for-all-azure-regions"></a>Resource Health はすべての Azure リージョンで利用できますか。 
Resource Health はすべての Azure geo で利用できます。

## <a name="how-is-resource-health-different-from-azure-status-or-the-service-health-dashboard"></a>Resource Health は Azure の状態や Service Health ダッシュボードと何が違うのでしょうか。
Resource Health の方が、Azure の状態や Service Health ダッシュボードと比べて具体的な細かい情報が得られます。

[Azure の状態](https://status.azure.com)と Service Health ダッシュボードでは広範な顧客 (Azure リージョンなど) に影響するサービスの問題に関する情報を通知しますが、Resource Health では特定のリソースのみに関連するより詳細なイベントを公開します。 たとえば、ホストが予期せず再起動するとき、Resource Health はそのホスト上で仮想マシンが実行されている顧客のみに警告します。

## <a name="do-i-need-to-activate-resource-health-for-each-resource"></a>リソースごとに Resource Health をアクティブにする必要がありますか。
いいえ。正常性情報は、Resource Health からすべてのリソースの種類に対して利用できます。 

## <a name="do-we-need-to-enable-resource-health-for-my-organization"></a>自分の組織で Resource Health を有効にする必要がありますか。
いいえ。  Azure Resource Health には Azure Portal 内でアクセスできます。セットアップ要件はありません。

## <a name="is-resource-health-available-free-of-charge"></a>Resource Health は無料で使用できますか。
はい。  Azure Resource Health は無料です。

## <a name="what-are-the-recommendations-that-resource-health-provides"></a>Resource Health で提示されている推奨事項とはどのようなものですか。
Resource Health では、正常性状態に基づいて、トラブルシューティングに費やされる時間の削減を目標とする推奨事項を提示しています。 使用可能なリソースの場合は、顧客が直面する最も一般的な問題を解決する方法に絞り込んだ推奨事項を提示します。 Azure の計画外のイベントが原因でリソースを使用できない場合、回復プロセス中と回復プロセス終了後のサポートに対象が絞り込まれます。 

## <a name="next-steps"></a>次のステップ

Resource Health に関する詳細情報を参照してください。
-  [Azure Resource Health の概要](Resource-health-overview.md)
-  [Azure Resource Health で利用できるリソースの種類と正常性チェック](resource-health-checks-resource-types.md)
