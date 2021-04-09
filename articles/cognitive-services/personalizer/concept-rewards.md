---
title: 報酬スコア - Personalizer
description: 報酬スコアは、パーソナル化の選択肢である RewardActionID がユーザーに対してどれほどの結果を生み出したかを示します。 報酬スコアの値は、ユーザーの動作の観測値に基づくビジネス ロジックによって判断されます。 Personalizer は、報酬を評価することにより、機械学習モデルをトレーニングします。
ms.service: cognitive-services
ms.subservice: personalizer
ms.date: 02/20/2020
ms.topic: conceptual
ms.openlocfilehash: f3249ba2089c3d9650aa46f665353ad392d0e773
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94365569"
---
# <a name="reward-scores-indicate-success-of-personalization"></a>報酬スコアは、パーソナル化の成功を示します

報酬スコアは、パーソナル化の選択肢である [RewardActionID](/rest/api/cognitiveservices/personalizer/rank/rank#response) がユーザーに対してどれほどの結果を生み出したかを示します。 報酬スコアの値は、ユーザーの動作の観測値に基づくビジネス ロジックによって判断されます。

Personalizer は、報酬を評価することにより、機械学習モデルをトレーニングします。

Azure portal で Personalizer リソースの既定の報酬スコアを構成する[方法](how-to-settings.md#configure-rewards-for-the-feedback-loop)について説明します。

## <a name="use-reward-api-to-send-reward-score-to-personalizer"></a>Reward API を使用した Personalizer への報酬スコアの送信

報酬は [Reward API](/rest/api/cognitiveservices/personalizer/events/reward) によって Personalizer に送信されます。 通常、報酬は、0 から 1 の数値です。 特定のシナリオでは負の報酬 (-1) を使用することができますが、強化学習 (RL) に馴染みがある場合にのみ使用してください。 Personalizer は、時間の経過に伴って報酬の合計が最大限可能な値に到達するようにモデルをトレーニングします。

報酬は、ユーザーの動作が行われた後に送信されます。これは数日後になることがあります。 イベントに報酬がない、または既定の報酬しかないと見なされるまで Personalizer が待機する最大の時間は、Azure portal の[報酬の待機時間](#reward-wait-time)で構成されます。

イベントの報酬スコアが **報酬の待機時間** 以内で受信されなかった場合、**既定の報酬** が適用されます。 通常、**[既定の報酬](how-to-settings.md#configure-reward-settings-for-the-feedback-loop-based-on-use-case)** はゼロになるように構成されます。


## <a name="behaviors-and-data-to-consider-for-rewards"></a>報酬のために考慮すべき動作とデータ

報酬スコアのコンテキストについて、次のシグナルと動作を考慮します。

* 選択肢 ("つまり X ということですか?") が含まれる場合の提案に対する直接のユーザー入力。
* セッションの長さ。
* セッション間の時間。
* ユーザーの相互作用のセンチメント分析。
* ボットからユーザーに有用性、正確度に関するフィードバックを求める直接の質問およびミニ アンケート。
* アラートへの応答、またはアラートへの応答の待機時間

## <a name="composing-reward-scores"></a>報酬スコアの作成

報酬スコアはビジネス ロジックで計算する必要があります。 スコアは、次のように表すことができます。

* 1 度送信された単一の数値
* 即座に送信されたスコア (0.8 など) と後から送信された追加スコア (通常 0.2)。

## <a name="default-rewards"></a>既定の報酬

Rank 呼び出し以降の期間である [報酬の待機時間](#reward-wait-time)以内に報酬が受信されていない場合、Personalizer は、**既定の報酬** をその Rank イベントに暗黙的に適用します。

## <a name="building-up-rewards-with-multiple-factors"></a>複数の要因による報酬の作成

有効なパーソナル化には、複数の要因に基づいて報酬スコアを作成できます。

たとえば、ビデオ コンテンツの一覧をパーソナライズするために以下の規則を適用できます。

|ユーザーの動作|部分的なスコア値|
|--|--|
|ユーザーが最上位の項目をクリックした。|+0.5 報酬|
|ユーザーがその項目の実際のコンテンツを開いた。|+0.3 報酬|
|ユーザーがコンテンツの 5 分または 30% のいずれか長い方を視聴した。|+0.2 報酬|
|||

続いて合計の報酬を API に送信できます。

## <a name="calling-the-reward-api-multiple-times"></a>複数回の Reward API の呼び出し

同じイベント ID を使用する Reward API を呼び出し、さまざまな報酬スコアを送信することもできます。 Personalizer は、これらの報酬を取得すると、Personalizer 構成内の指定に従ってこれらを集計することにより、そのイベントの最終的な報酬を決定します。

集計値:

*  **First**: イベントについて最初に受信した報酬スコアを受け入れ、残りを破棄します。
* **Sum**: eventId について収集されたすべての報酬スコアを受け入れ、それらを一緒に追加します。

**報酬の待機時間** 後に受信されたイベントのすべての報酬は破棄され、モデルのトレーニングには影響しません。

報酬スコアを加算した結果、最終的な報酬が想定されるスコアの範囲外になる場合があります。 これによりサービスが失敗することはありません。

## <a name="best-practices-for-calculating-reward-score"></a>報酬スコアを計算するためのベスト プラクティス

* **成功したパーソナル化の真のインジケーターを考慮する**: クリック数の観点で考えることは簡単ですが、適切な報酬は、人々に "*する*" ように望むものではなく、ユーザーに "*達成する*" ように望むものに基づきます。  たとえば、クリック数で報酬を与える場合、クリックベイトの傾向があるコンテンツを選択してしまうことがあります。

* **パーソナル化がどれだけ適切に機能していたかに関する報酬スコアを使用する**: おすすめの映画のパーソナル化は、うまくいけば、ユーザーが映画を視聴し高評価を付けるという結果になります。 映画の評価はおそらく、多くの点 (演技の質、ユーザーの気分) によって異なるため、*パーソナル化* がどれだけうまくいったかに関する適切な報酬シグナルにはなりません。 ただし、映画の最初の数分間を視聴したユーザーは、パーソナル化の効果を示すより適切なシグナルになる可能性があり、5 分後に 1 の報酬を送信すれば適切なシグナルになります。

* **報酬は RewardActionID にのみ適用する**: Personalizer は報酬を適用し、RewardActionID で指定されたアクションの有効性を理解します。 他のアクションを表示することにした場合、ユーザーがそれらをクリックしても報酬はゼロになります。

* **意図しない結果を考慮する**: [倫理と責任ある使用](ethics-responsible-use.md)での責任ある結果をもたらす報酬関数を作成します。

* **増分報酬を使用する**: ユーザーのより小さな動作に対して部分的な報酬を追加することは、Personalizer がより適切な報酬を達成する場合に役立ちます。 この増分報酬により、アルゴリズムは、最終的な目的の動作へのユーザーのエンゲージメントに近づいていることがわかります。
    * 映画の一覧を表示している場合、ユーザーが最初の映画にしばらくカーソルを置いて詳細を見ていれば、何らかのユーザー エンゲージメントが起きたと判断できます。 動作は、0.1 の報酬スコアでカウントできます。
    * ユーザーがページを開いた後に終了した場合、報酬は 0.2 になります。

## <a name="reward-wait-time"></a>Reward wait time (報酬の待機時間)

Personalizer は、Rank 呼び出しの情報を、モデルをトレーニングするために Reward 呼び出しで送信された報酬と関連付けます。 これらは異なる時刻に到着することがあります。 Rank 呼び出しが非アクティブなイベントとして行われ、後からアクティブ化された場合でも、Personalizer は、Rank 呼び出しが行われたときから一定期間待機します。

**報酬の待機時間** の有効期限が切れ、報酬情報が存在していない場合、既定の報酬がトレーニングのためにそのイベントに適用されます。 最大待機時間は、6 日です。

## <a name="best-practices-for-reward-wait-time"></a>報酬の待機時間のためのベスト プラクティス

より優れた結果を得るために、以下の推奨事項に従ってください。

* 報酬の待機時間はできるだけ短くしますが、ユーザーのフィードバックを得るための時間は十分に取っておきます。

* フィードバックの取得に必要な時間よりも短い期間を選択しないでください。 たとえば、ユーザーが 1 分間ビデオを観た後でいくつかの報酬が到着した場合、実験の長さは少なくともその 2 倍にする必要があります。

## <a name="next-steps"></a>次のステップ

* [強化学習](concepts-reinforcement-learning.md)
* [Rank API を試す](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank/console)
* [Reward API を試す](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward)