---
title: Internet Analyzer に関する FAQ | Microsoft Docs
description: Azure Internet Analyzer に関する FAQ。
services: internet-analyzer
author: diego-perez-botero
ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
ms.openlocfilehash: a4a5b058666fab3e9048a7d92726dccd1360ff37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "74184249"
---
# <a name="azure-internet-analyzer-faq-preview"></a>Azure Internet Analyzer の FAQ (プレビュー)

これは Azure Internet Analyzer の FAQ です。他に質問がある場合は、[フィードバック フォーラム](https://aka.ms/internetAnalyzerFeedbackForum) にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="how-do-i-participate-in-the-preview"></a>プレビューに参加するにはどうすればよいですか?

プレビューは一部のお客様がご利用いただけます。 プレビューへの参加にご興味があれば、次の操作を行ってください。

1. [Azure portal](https://ms.portal.azure.com) にサインインします。
2. **[サブスクリプション]** ページに移動します。
3. Internet Analyzer の使用を計画している Azure サブスクリプションをクリックします。
4. サブスクリプションの **[リソース プロバイダー]** 設定に移動します。
5. **[Microsoft.Network]** を検索し、**[登録]** (または **[再登録]**) ボタンをクリックします。
![アクセス要求](./media/ia-faq/request-preview-access.png)

6. アクセス要求の作成に使用された電子メール アドレスと Azure サブスクリプション ID を提供することによって[承認を要求します](https://aka.ms/internetAnalyzerContact)。
7. 要求が承認されると、電子メールの確認メッセージが表示され、新しく許可された Azure サブスクリプションから Internet Analyzer リソースを作成/更新/変更できるようになります。

## <a name="do-i-need-to-embed-the-client-to-run-a-test"></a>テストを実行するには、クライアントを埋め込む必要がありますか?

はい。ユーザー独自のメトリックを収集するには、Internet Analyzer クライアントをアプリケーションにインストールする必要があります。 [クライアントをインストールする方法をご覧ください。](internet-analyzer-embed-client.md) 

## <a name="do-i-get-billed-for-participating-in-the-preview"></a>プレビューへの参加は課金対象ですか?
いいえ。プレビューでは Azure Internet Analyzer は無料でご利用いただけます。 プレビュー中は、サービス レベル アグリーメントはありません。

## <a name="what-scenarios-is-internet-analyzer-designed-to-address"></a>Internet Analyzer はどのようなシナリオに対応していますか?

Internet Analyzer は、ユーザーに基づいてネットワーク パフォーマンスの分析情報を提供するように設計されています。 ユーザーにとって最適なパフォーマンスを決定できるようにするために、Internet Analyzer は、ユーザーの個別設定を使用して 2 つのインターネット エンドポイントのパフォーマンスを比較します。 Internet Analyzer は多くの質問に答えることができますが、特に一般的なものは次のとおりです。

* クラウドに移行すると、パフォーマンスにどのような影響があるか。 
    * *推奨されるテスト: カスタム (現在のオンプレミス インフラストラクチャ) vs.Azure (事前構成済みの任意のエンドポイント)*
* 自分のデータをエッジに置いた場合とデータ センターに置いた場合では、どのような価値の違いがあるか。 
    *  *推奨されるテスト: Azure vs.Azure Front Door、Azure vs. Microsoft の Azure CDN*
* Azure Front Door のパフォーマンス上の利点は何か。
    *  *推奨されるテスト: カスタム/Azure/CDN vs. Azure Front Door*
* Microsoft の Azure CDN のパフォーマンス上の利点は何か。 
    *  *推奨されるテスト: カスタム/Azure/AFD vs. Microsoft の Azure CDN*
* Microsoft の Azure CDN はどのように積み重ねられているか。 
    *  *推奨されるテスト: カスタム (他の CDN エンドポイント) vs. Microsoft の Azure CDN*
* 各リージョンの自分のエンドユーザーのグループに最適なクラウドは何か。 
    *  *推奨されるテスト: カスタム (他のクラウドサービス) vs. Azure (事前構成済みの任意のエンドポイント)*

## <a name="which-tests-can-i-run-in-preview"></a>どのテストをプレビューで実行できますか?

Internet Analyzer で作成する各テストは、エンドポイント A とエンドポイント B の 2 つのエンドポイントで構成されます。以下のいずれかの組み合わせをテストとして実行できます。  
* 2 つの事前構成済みエンドポイント
* 1 つのカスタム エンドポイントと 1 つの事前構成済みエンドポイント
* 2 つの [カスタム エンドポイント](internet-analyzer-custom-endpoint.md) (1 つのカスタム エンドポイントは Azure に存在する必要があります)。

プレビュー中は、次の事前構成済みのエンドポイントを使用できます。
* **Azure リージョン**
    * ブラジル南部
    * インド中部
    * 米国中部
    * 東アジア
    * 米国東部
    * 西日本
    * 北ヨーロッパ
    * 南アフリカ北部
    * 東南アジア
    * アラブ首長国連邦北部
    * 英国西部  
    * 西ヨーロッパ
    * 米国西部
    * 米国西部 2
* **複数の Azure リージョンの組み合わせ**
    * 米国東部、ブラジル南部
    * 米国東部、東アジア
    * 西ヨーロッパ、ブラジル南部
    * 西ヨーロッパ、東南アジア
    * 西ヨーロッパ、アラブ首長国連邦北部
    * 米国西部、米国東部
    * 米国西部、西ヨーロッパ
    * 米国西部、アラブ首長国連邦北部
    * 西ヨーロッパ、アラブ首長国連邦北部、東南アジア
    * 米国西部、西ヨーロッパ、東アジア
    * 米国西部、北ヨーロッパ、東南アジア、アラブ首長国連邦北部、南アフリカ北部 
* **Azure + Azure Front Door** - 上記のいずれか 1 つまたは複数の Azure リージョンの組み合わせに対してデプロイされます
* **Azure + Microsoft の Azure CDN** - 上記のいずれか 1 つの Azure リージョンの組み合わせに対してデプロイされます
* **Azure + Azure Traffic Manager** - 上記のいずれか複数の Azure リージョンの組み合わせに対してデプロイされます

## <a name="how-is-internet-analyzer-different-from-other-monitoring-services-provided-by-azure"></a>Internet Analyzer は、Azure によって提供される他の監視サービスとどのように違いますか。

Internet Analyzer は、エンドユーザーのパフォーマンスを理解し、パフォーマンスを向上させるための意思決定に役立ちます。 他の Azure 監視ツールは Azure サービスに関する分析情報を提供しますが、Internet Analyzer はユーザーのエンドツーエンドのインターネット パフォーマンスの測定に重点を置いています。

## <a name="how-is-measurement-data-handled-by-internet-analyzer"></a>Internet Analyzer における測定データの処理方法

Azure には[強力なセキュリティ プロセスがあり、広範囲のコンプライアンス標準を満たします](https://azure.microsoft.com/support/trust-center/)。 あなたとあなたが指名したチームだけがあなたのデータにアクセスできます。 Microsoft のスタッフには、特定の状況下でのみ、あなたに通知した上で限られたアクセスが与えられます。 これは、転送中および保存時に暗号化されます。

## <a name="next-steps"></a>次の手順

詳細については、[Internet Analyzer の概要](internet-analyzer-overview.md)に関する記事を参照してください。
