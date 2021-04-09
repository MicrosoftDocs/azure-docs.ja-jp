---
title: Azure Security Center におけるアダプティブ アプリケーション制御
description: このドキュメントは、Azure マシンで実行されるアプリケーションの許可リストを定義するために、Azure Security Center で適応型アプリケーション制御を使用する場合に役に立ちます。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/07/2021
ms.author: memildin
ms.openlocfilehash: de5c5fbb6673d022517bba2486ed4aa1f739d19b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439580"
---
# <a name="use-adaptive-application-controls-to-reduce-your-machines-attack-surfaces"></a>適応型アプリケーション制御を使用して、マシンの攻撃対象領域を減らす

Azure Security Center の適応型アプリケーション制御の利点と、このデータ ドリブンでインテリジェントな機能を使用してセキュリティを強化する方法について学習します。


## <a name="what-are-security-centers-adaptive-application-controls"></a>Security Center の適応型アプリケーション制御とは

適応型アプリケーション制御は、マシンの既知の安全なアプリケーションの許可リストを定義するためのインテリジェントで自動化されたソリューションです。 

多くの場合、組織には、同じプロセスを定期的に実行するマシンのコレクションがあります。 Security Center によって機械学習が使用され、マシン上で実行されているアプリケーションが分析され、既知の安全なソフトウェアのリストが作成されます。 許可リストは特定の Azure ワークロードに基づいています。以下の手順を使用して、推奨事項をさらにカスタマイズすることができます。

適応型アプリケーション制御を有効にして構成した場合、安全なものとして定義したもの以外のアプリケーションが実行されると、セキュリティ アラートが表示されます。


## <a name="what-are-the-benefits-of-adaptive-application-controls"></a>適応型アプリケーション制御の利点とは

既知の安全なアプリケーションのリストを定義し、それ以外のものが実行されたときにアラートを生成すると、以下のような複数のセキュリティ強化の目標を達成できます。

- 潜在的なマルウェア (マルウェア対策ソリューションでは見逃される可能性のあるものを含む) を識別する
- ライセンスのあるソフトウェアのみの使用を規定するローカル セキュリティ ポリシーのコンプライアンスを強化する
- 古い、あるいはサポートされていないアプリケーションの実行を避ける
- 組織で禁止されている特定のソフトウェアが使用されないようにする
- 機密データにアクセスするアプリの監視を強化する

現時点では、利用できる強制オプションはありません。 適応型アプリケーション制御では、安全なものとして定義したもの以外のアプリケーションが実行されると、セキュリティ アラートが表示されることを想定しています。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般提供 (GA)|
|価格:|[Azure Defender for servers](defender-for-servers-introduction.md) が必要|
|サポートされているマシン:|![Yes](./media/icons/yes-icon.png) Windows および Linux を実行する Azure と Azure 以外のマシン<br>![Yes](./media/icons/yes-icon.png) [Azure Arc](../azure-arc/index.yml) マシン|
|必要なロールとアクセス許可:|**セキュリティ閲覧者** および **閲覧者** のいずれのロールでも、グループと、既知の安全なアプリケーションのリストを表示できます<br>**共同作成者** および **セキュリティ管理者** のいずれのロールでも、グループと、既知の安全なアプリケーションのリストを編集できます|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![Yes](./media/icons/yes-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||



## <a name="enable-application-controls-on-a-group-of-machines"></a>マシンのグループに対してアプリケーション制御を有効にする

類似した一連のアプリケーションを一貫して実行する、サブスクリプション内のマシンのグループが Security Center によって特定された場合は、次の推奨事項が示されます: **安全なアプリケーションの定義のために適応型アプリケーション制御をマシンで有効にする必要がある**。

推奨事項を選択するか、適応型アプリケーション制御ページを開いて、推奨される既知の安全なアプリケーションのリストとマシンのグループを表示します。

1. Azure Defender ダッシュボードを開き、高度な保護領域から **[適応型アプリケーション制御]** を選択します。

    :::image type="content" source="./media/security-center-adaptive-application/opening-adaptive-application-control.png" alt-text="Azure ダッシュボードから適応型アプリケーション制御を開く" lightbox="./media/security-center-adaptive-application/opening-adaptive-application-control.png":::

    **[適応型アプリケーション制御]** ページが開き、VM が次のタブにグループ化されます。

    - **構成済み** - アプリケーションの許可リストが既に定義されているマシンのグループ。 グループごとに、[構成済み] タブには以下の内容が示されます。
        - グループ内のマシンの数
        - 最近のアラート

    - **推奨** - 同じアプリケーションを一貫して実行し、許可リストが構成されていないマシンのグループ。 これらのグループに対して適応型アプリケーション制御を有効にすることをお勧めします。
    
      > [!TIP]
      > "REVIEWGROUP" というプレフィックスが付いたグループ名が表示される場合は、部分的に一貫性のあるアプリケーションのリストがあるマシンが含まれます。 Security Center でパターンは表示できませんが、このグループを参照し、「[グループの適応型アプリケーション制御規則の編集](#edit-a-groups-adaptive-application-controls-rule)」の説明に従って、いくつかの適応型アプリケーション制御規則を "_お客様_" が手動で定義できるかどうかを確認することが推奨されます。
      >
      > 「[グループ間でマシンを移動する](#move-a-machine-from-one-group-to-another)」の説明に従って、このグループから他のグループにマシンを移動することもできます。

    - **推奨なし** - アプリケーションの許可リストが定義されておらず、機能がサポートされていないマシン。 マシンは次の理由により、このタブに表示される場合があります。
      - Log Analytics エージェントが欠落している
      - Log Analytics エージェントによってイベントが送信されていない
      - GPO またはローカル セキュリティ ポリシーによって既存の [AppLocker](/windows/security/threat-protection/windows-defender-application-control/applocker/applocker-overview) ポリシーが有効になっている Windows マシンである

      > [!TIP]
      > マシンのグループごとに固有の推奨事項を定義するには、Security Center では少なくとも 2 週間分のデータが必要です。 最近作成されたか、Azure Defender でのみ最近有効にされたサブスクリプションに属しているマシンは、 **[推奨なし]** タブの下に表示されます。


1. **[推奨]** タブを開きます。推奨される許可リストがあるマシンのグループが表示されます。

   ![[推奨] タブ](./media/security-center-adaptive-application/adaptive-application-recommended-tab.png)

1. グループを選びます。 

1. 新しい規則を構成するには、この **[アプリケーションの制御に関する規則の構成]** ページのさまざまなセクションとその内容を確認します。これは、特定のマシンのグループに固有のものです。

   ![新しい規則を構成する](./media/security-center-adaptive-application/adaptive-application-create-rule.png)

   1. **マシンの選択** - 既定では、識別されたグループ内のすべてのマシンが選択されます。 いずれかの選択を解除すると、それらはこの規則から除外されます。
   
   1. **推奨アプリケーション** - このグループ内のマシンに共通し、実行を許可することが推奨されるアプリケーションのこのリストを確認します。
   
   1. **その他のアプリケーション** - このグループ内のマシンでの表示頻度が低いか、あるいは利用可能であることがわかっているアプリケーションのこのリストを確認します。 警告アイコンは、アプリケーションの許可リストをバイパスするために、特定のアプリケーションが攻撃者によって使用される可能性があることを示します。 これらのアプリケーションを慎重に確認することをお勧めします。

      > [!TIP]
      > どちらのアプリケーション リストにも、特定のアプリケーションを特定のユーザーに制限するオプションが含まれています。 可能な限り、最小限の特権の原則を採用してください。
      > 
      > アプリケーションは、発行元によって定義されます。アプリケーションに発行元情報がない (署名されていない) 場合、特定のアプリケーションの完全なパスに対してパス規則が作成されます。

   1. 規則を適用するには、 **[監査]** を選択します。 




## <a name="edit-a-groups-adaptive-application-controls-rule"></a>グループの適応型アプリケーション制御規則の編集

組織内の既知の変更により、マシンのグループの許可リストを編集することができます。 

マシン グループの規則を編集するには、次のようにします。

1. Azure Defender ダッシュボードを開き、高度な保護領域から **[適応型アプリケーション制御]** を選択します。

1. **[構成済み]** タブから、規則を編集するグループを選択します。

1. [マシンのグループに対する適応型アプリケーション制御の有効化](#enable-application-controls-on-a-group-of-machines)に関する説明に従って、 **[アプリケーションの制御に関する規則の構成]** ページのさまざまなセクションを確認します。

1. 必要に応じて、1 つまたは複数のカスタム規則を追加します。

   1. **[規則の追加]** を選択します。

      ![カスタム規則を追加する](./media/security-center-adaptive-application/adaptive-application-add-custom-rule.png)

   1. 既知の安全なパスを定義する場合は、 **[規則の種類]** を [パス] に変更して、1 つのパスを入力します。 パスにはワイルドカードを含めることができます。
   
      > [!TIP]
      > パスのワイルドカードが役立つ場合があるシナリオには、次のようなものがあります。
      > 
      > * パスの末尾でワイルドカードを使用し、そのフォルダーとサブフォルダー内のすべての実行可能ファイルを許可します。
      > * パスの途中でワイルドカードを使用し、変化するフォルダー名を持つ既知の実行可能ファイルの名前 (既知の実行可能ファイルを含む個人ユーザー フォルダーや自動生成されたフォルダー名など) を有効にします。
  
   1. 許可されるユーザーと保護されるファイルの種類を定義します。

   1. 規則の定義が完了したら、 **[追加]** を選択します。

1. 変更を適用するには、 **[保存]** を選択します。


## <a name="review-and-edit-a-groups-settings"></a>グループの設定を確認して編集する

1. グループの詳細と設定を表示するには、 **[グループ設定]** を選択します

    このウィンドウには、グループの名前 (変更可能)、OS の種類、場所、およびその他の関連する詳細が表示されます。

    :::image type="content" source="./media/security-center-adaptive-application/adaptive-application-group-settings.png" alt-text="適応型アプリケーション制御の [グループ設定] ページ" lightbox="./media/security-center-adaptive-application/adaptive-application-group-settings.png":::

1. 必要に応じて、グループの名前やファイルの種類の保護モードを変更します。

1. **[適用]** を選択し、 **[保存]** を選択します。



## <a name="respond-to-the-allowlist-rules-in-your-adaptive-application-control-policy-should-be-updated-recommendation"></a>[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある] 推奨事項への対応

Security Center の機械学習で、以前は許可されていなかった、正当である可能性のある動作が識別された場合、この推奨事項が表示されます。 誤検知アラートの数を減らすために、推奨事項で既存の定義の新しい規則が提案されます。

問題を修復するには、次のようにします。

1. 推奨事項ページから、 **[適応型アプリケーション制御ポリシーの許可リスト ルールを更新する必要がある]** 推奨事項を選択し、新しく識別された、正当である可能性のある動作のグループを表示します。

1. 規則を編集するグループを選択します。

1. [マシンのグループに対する適応型アプリケーション制御の有効化](#enable-application-controls-on-a-group-of-machines)に関する説明に従って、 **[アプリケーションの制御に関する規則の構成]** ページのさまざまなセクションを確認します。

1. 変更を適用するには、 **[監査]** を選択します。




## <a name="audit-alerts-and-violations"></a>アラートと違反の監査

1. Azure Defender ダッシュボードを開き、高度な保護領域から **[適応型アプリケーション制御]** を選択します。

1. 最近のアラートが示されたマシンのグループを表示するには、 **[構成済み]** タブに一覧表示されているグループを確認します。

1. さらに調査するには、グループを選択します。

   ![最近のアラート](./media/security-center-adaptive-application/recent-alerts.png)

1. 詳細および影響を受けるマシンのリストを表示するには、アラートを選択します。

    アラート ページには、アラートの詳細が表示され、脅威を軽減するための推奨事項と共に **[アクションの実行]** リンクが表示されます。

    :::image type="content" source="media/security-center-adaptive-application/adaptive-application-alerts-start-time.png" alt-text="適応型アプリケーション制御アラートの開始時刻":::

    > [!NOTE]
    > 適応型アプリケーション制御では、12 時間ごとにイベントが計算されます。 アラート ページに表示される [アクティビティの開始時刻] は、疑わしいプロセスがアクティブだった時間では **なく**、適応型アプリケーション制御によってアラートが作成された時刻です。


## <a name="move-a-machine-from-one-group-to-another"></a>グループ間でマシンを移動する

グループ間でマシンを移動すると、適用されていたアプリケーション制御ポリシーが移動先のグループの設定に変更されます。 構成されたグループから構成されていないグループにマシンを移動することもできます。これにより、マシンに適用されていたアプリケーション制御規則がすべて削除されます。

1. Azure Defender ダッシュボードを開き、高度な保護領域から **[適応型アプリケーション制御]** を選択します。

1. **[適応型アプリケーション制御]** ページの **[構成済み]** タブから、移動対象のマシンを含むグループを選択します。

1. **[構成されたマシン]** のリストを開きます。

1. 行の末尾にある 3 つの点をクリックしてマシンのメニューを開き、 **[移動]** を選択します。 **[Move machine to a different group]\(マシンを別のグループに移動する\)** ペインが開きます。

1. 移動先グループを選び、 **[Move machine]\(マシンを移動\)** を選択します。

1. 変更を保存するには、 **[保存]** を選択します。





## <a name="manage-application-controls-via-the-rest-api"></a>REST API を使用したアプリケーション制御の管理 

適応型アプリケーション制御をプログラムで管理するには、REST API を使用します。 

関連する API ドキュメントは、[Security Center の API ドキュメントの「適応型アプリケーション制御」セクション](/rest/api/securitycenter/adaptiveapplicationcontrols)で参照できます。

REST API から使用できる関数をいくつか以下に示します。

* **List** を使用すると、すべてのグループの推奨事項が取得され、各グループのオブジェクトを含む JSON が提供されます。

* **Get** を使用すると、すべての推奨データ (つまり、マシン、発行元またはパスの規則のリスト) を含む JSON が取得されます。

* **Put** を使用すると、規則が構成されます (この要求の本文として、**Get** で取得した JSON を使用)。
 
   > [!IMPORTANT]
   > **Put** 関数には、Get コマンドによって返される JSON に含まれるものより少ない数のパラメーターが必要です。
   >
   > Put 要求で JSON を使用する前に、recommendationStatus、configurationStatus、issues、location、sourceSystem の各プロパティを削除してください。


## <a name="faq---adaptive-application-controls"></a>FAQ - 適応型アプリケーション制御

### <a name="are-there-any-options-to-enforce-the-application-controls"></a>アプリケーション制御を強制するためのオプションはありますか。
現時点では、利用できる強制オプションはありません。 適応型アプリケーション制御では、安全なものとして定義したもの以外のアプリケーションが実行されると、**セキュリティ アラート** が表示されることを想定しています。 これらにはさまざまな利点があり (「[適応型アプリケーション制御の利点とは](#what-are-the-benefits-of-adaptive-application-controls)」)、このページに示すように高度なカスタマイズが可能です。

 

## <a name="next-steps"></a>次の手順
このドキュメントでは、Azure と Azure 以外のマシンで実行されるアプリケーションの許可リストを定義するために、Azure Security Center で適応型アプリケーション制御を使用する方法を学習しました。 Security Center の他のいくつかのクラウド ワークロード保護機能の詳細については、次のページを参照してください。

* [Just-In-Time (JIT) VM アクセスについて](just-in-time-explained.md)
* [Azure Kubernetes クラスターのセキュリティ保護](defender-for-kubernetes-introduction.md)