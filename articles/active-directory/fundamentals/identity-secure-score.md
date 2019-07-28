---
title: ID セキュリティ スコアとは - Azure Active Directory
description: ID セキュリティ スコアを使用して、お使いのディレクトリのセキュリティ体制を強化する方法
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: microsoftguyjflo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65988675"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Azure Active Directory の ID セキュリティ スコアとは

Azure AD テナントはどの程度セキュリティ保護されていますか。 この質問にどう答えていいかわからない場合、この記事で、ID セキュリティ スコアが、ID セキュリティ体制を監視したり強化したりするうえでどのように役立つかを確認できます。

## <a name="what-is-an-identity-secure-score"></a>ID セキュリティ スコアとは

ID セキュリティ スコアは、セキュリティに関する Microsoft のベスト プラクティスの推奨事項にどれだけ適合しているかを示す指標として機能する 1 から 223 までの数値です。 ID セキュリティ スコアの各改善アクションが、ご自身の固有の構成に合わせて調整されています。  

![セキュリティ スコア](./media/identity-secure-score/identity-secure-score-overview.png)

このスコアは、次のために役立ちます。

- ID セキュリティ体制を客観的に測定する
- ID セキュリティの強化を計画する
- 強化の成功を確認する

このスコアや関連情報には、ID セキュリティ スコア ダッシュボードでアクセスできます。 このダッシュボードには、次の内容が表示されます。

- ご自身の ID セキュリティ スコア
- 比較グラフ。ご自身の ID セキュリティ スコアが、同じ業界および同じ規模の他のテナントのスコアと比較されています
- 傾向グラフ。一定期間にわたる ID セキュリティ スコアの変化が示されています
- 考えられる改善点の一覧

強化アクションに従うことによって、次のことが可能になります。

- セキュリティ体制を強化し、スコアを改善する
- お使いの ID への投資の一環としてご自身の組織が使用できる機能を活用する

## <a name="how-do-i-get-my-secure-score"></a>セキュリティ スコアを取得する方法

ID セキュリティ スコアは、Azure AD のすべてのエディションで使用できます。 このスコアにアクセスするには、[Azure AD の概要ダッシュボード](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore)に移動してください。

## <a name="how-does-it-work"></a>それはどのように機能しますか?

48 時間ごとに、Azure はセキュリティ構成を調べ、その設定を推奨されるベスト プラクティスと比較します。 この評価の結果に基づいて、ご自身のディレクトリの新しいスコアが計算されます。 ご自身のセキュリティ構成がベスト プラクティス ガイダンスに完全には適合していない、また、強化アクションに部分的にしか従っていない可能性があります。 これらのシナリオで獲得できるのは、コントロールの最大スコアの一部のみになります。

各推奨事項は、Azure AD の構成に基づいて測定されます。 ベスト プラクティスの推奨事項を有効にするためにサードパーティ製品を使用している場合は、強化アクションの設定でこの構成を示すことができます。 推奨事項が実際の環境に適用されない場合は、それらの推奨事項が無視されるように設定するオプションも使用できます。 無視された推奨事項は、スコアの計算には使用されません。

![アクションを無視するか、サード パーティ対象アクションとしてマークする](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>それはどのように役立つか

セキュリティ スコアは、次のために役立ちます。

- ID セキュリティ体制を客観的に測定する
- ID セキュリティの強化を計画する
- 強化の成功を確認する

## <a name="what-you-should-know"></a>知っておくべきこと

### <a name="who-can-use-the-identity-secure-score"></a>ID セキュリティ スコアは誰が使用できますか?

ID セキュリティ スコアは、次のロールが使用できます。

- 全体管理者
- セキュリティ管理者
- セキュリティ閲覧者

### <a name="how-are-controls-scored"></a>コントロールのスコアはどのように付けられますか?

コントロールのスコアは 2 つの方法で付けられます。 1 つはバイナリ形式でスコア付けする方法です。この場合、機能または設定が Microsoft の推奨事項に基づいて構成されていると、100% のスコアを獲得できます。 もう 1 つの方法では、全体の構成の割合としてスコアが計算されます。 たとえば、改善の推奨事項に、すべてのユーザーを MFA で保護すれば 30 ポイントを獲得できることが記載されている場合、合計ユーザー数 100 人のうち 5 人しか保護されていないと、獲得できるのは部分スコアの約 2 ポイントです (5 (保護されたユーザーの数) / 100 (合計ユーザー数) × 30 (最大ポイント) = 2 ポイント (部分スコア))。

### <a name="what-does-not-scored-mean"></a>[スコア付けなし] とはどういう意味ですか?

[スコア付けなし] というラベルが付いたアクションは、組織で実行できるが、ツールに (まだ) 組み込まれていないためにスコア付けされないアクションです。 そのため、引き続きセキュリティを強化できますが、現時点でこれらのアクションの実行には意味がありません。

### <a name="how-often-is-my-score-updated"></a>このスコアはどの程度の頻度で更新されますか?

このスコアは、1 日に 1 回 (太平洋標準時午前 1:00 頃) 計算されます。 測定されたアクションに変更を加えた場合、このスコアは翌日に自動的に更新されます。 変更がスコアに反映されるまでに、最大 48 時間かかります。

### <a name="my-score-changed-how-do-i-figure-out-why"></a>スコアが変更されました。 その理由を見つけるにはどうしたらよいですか?

[Microsoft 365 セキュリティ センター](https://security.microsoft.com/)をご覧ください。ここには、ご自身の Microsoft セキュリティ スコアの詳細情報が示されています。 ご自身のセキュリティ スコアに対するすべての変更の詳細を、履歴タブで確認することができます。

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>セキュリティ スコアは侵害されるリスクを測定しますか?

手短に言えば、しません。 セキュリティ スコアは、侵害される可能性の絶対的な尺度を表していません。 これは、侵害されるリスクを相殺できる機能をどの程度採用しているかを表しています。 侵害されないようにすることを保証できるサービスは存在せず、セキュリティ スコアをどのような保証としても解釈すべきではありません。

### <a name="how-should-i-interpret-my-score"></a>このスコアをどのように解釈したらよいですか?

ユーザーには、推奨されるセキュリティ機能を構成したり、セキュリティ関連のタスク (レポートの参照など) を実行したりするためのポイントが提供されます。 一部のアクションは、部分的な完了としてスコア付けされます (ユーザーのための多要素認証 (MFA) の有効化など)。 セキュリティ スコアは、使用している Microsoft セキュリティ サービスを直接表しています。 セキュリティは、使いやすさとのバランスを取る必要があることに注意してください。 すべてのセキュリティ コントロールには、ユーザーに影響するコンポーネントがあります。 ユーザーへの影響が少ないコントロールは、ユーザーの日常操作にほとんど、またはまったく影響しません。

スコアの履歴を表示するには、[Microsoft 365 セキュリティ センター](https://security.microsoft.com/)に移動して、ご自身の全体的な Microsoft セキュリティ スコアをご確認ください。 全体的なセキュリティ スコアへの変更を確認するには、[履歴の表示] をクリックします。 特定の日付を選択すると、その日どのコントロールが有効になっていて、コントロールごとにどれだけのポイントを得たかを確認できます。

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>ID セキュリティ スコアは Office 365 のセキュリティ スコアにどのように関連していますか?

[Microsoft のセキュリティ スコア](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)には、次の 5 つのコントロールとスコア カテゴリがあります。

- ID
- データ
- デバイス
- インフラストラクチャ
- アプリケーション

ID セキュリティ スコアは、Microsoft のセキュリティ スコアの ID 部分を表しています。 この重複は、ID セキュリティ スコアと Microsoft の ID スコアに関する推奨事項が同じであることを意味します。

## <a name="next-steps"></a>次の手順

[Microsoft セキュリティ スコアの詳細を確認する](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
