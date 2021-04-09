---
title: チュートリアル - Azure IoT Central を使用して患者の継続的なモニタリング アプリを作成する | Microsoft Docs
description: このチュートリアルでは、Azure IoT Central アプリケーション テンプレートを使用して患者の継続的なモニタリング アプリケーションを作成する方法について説明します。
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 56ff43980aafc75d5936b86c6ba2cd400311c5fa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719102"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>チュートリアル:患者の継続的なモニタリング アプリ テンプレートのデプロイとウォークスルー

このチュートリアルでは、ソリューション ビルダーを対象に、IoT Central の患者の継続的なモニタリング アプリケーション テンプレートをデプロイして作業を開始する方法について説明します。 テンプレートをデプロイして使用する方法をご覧ください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * アプリケーション テンプレートを作成する
> * アプリケーション テンプレートのウォークスルーを行う

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお勧めします。 また、7 日間無料の試用版を使用することもできます。 Azure サブスクリプションがない場合は、[Azure サインアップ ページ](https://aka.ms/createazuresubscription)で作成できます。

## <a name="create-an-application-template"></a>アプリケーション テンプレートを作成する

[Azure IoT Central のアプリケーション マネージャーの Web サイト](https://apps.azureiotcentral.com/)に移動します。 左側のナビゲーション バーから **[ビルド]** を選択し、 **[Healthcare]\(ヘルスケア\)** タブを選択します。

:::image type="content" source="media/app-manager-health.png" alt-text="ヘルスケア アプリのテンプレート":::

**[アプリの作成]** ボタンを選択してアプリケーションの作成を開始し、Microsoft の個人用アカウント、職場アカウント、または学校アカウントを使用してサインインします。 **[新しいアプリケーション]** ページが表示されます。

![ヘルスケア アプリケーションの作成](media/app-manager-health-create.png)

![ヘルスケア アプリケーションの作成の課金情報](media/app-manager-health-create-billinginfo.png)

アプリケーションを作成するには、次のようにします。

1. Azure IoT Central が、ユーザーが選択したテンプレートに基づいて自動的にアプリケーション名を提案します。 この名前をそのまま使用することも、独自のわかりやすいアプリケーション名を入力することもできます (**患者の継続的なモニタリング** など)。 また、Azure IoT Central は、アプリケーション名に基づいて、一意の URL プレフィックスも生成します。 この URL プレフィックスは、もっと覚えやすいものに変更することもできます。

2. "*無料*" 料金プランまたはいずれかの "*標準*" 料金プランを選択して、アプリケーションを作成できます。 無料プランを使用して作成するアプリケーションは、有効期限が切れるまでの 7 日間は無料で、最大で 5 つの無料デバイスを使用できます。 有効期限が切れる前にいつでも、アプリケーションを無料プランから標準料金プランに切り替えることができます。 無料プランを選択する場合は、連絡先情報を入力し、Microsoft から情報やヒントを受け取るかどうかを選択します。 標準プランを使用して作成するアプリケーションでは、最大 2 つの無料デバイスがサポートされ、課金のために Azure サブスクリプション情報の入力が必要になります。

3. ページの下部にある **[作成]** を選択して、アプリケーションをデプロイします。

## <a name="walk-through-the-application-template"></a>アプリケーション テンプレートのウォークスルーを行う

### <a name="dashboards"></a>ダッシュボード

アプリ テンプレートをデプロイすると、最初に **[Lamna in-patient monitoring dashboard]\(Lamna 入院患者のモニタリング ダッシュボード\)** が表示されます。 Lamna ヘルスケアは、次の 2 つの病院を含む架空の病院システムです。Woodgrove 病院と Burkville 病院。 Woodgrove 病院のオペレーター ダッシュボードの機能を次に示します。

* デバイスの **バッテリ レベル** や **接続** 状態など、デバイスのテレメトリとプロパティを確認する。

* Smart Vitals Patch デバイスの **間取り図** と場所を表示する。

* 新しい患者の Smart Vitals Patch を **再プロビジョニング** する。

* 病院のケア チームが患者を追跡するために見る可能性がある **プロバイダー ダッシュボード** の例を表示する。

* デバイスが入院患者と遠隔のどちらのシナリオに使用されているかを示すために、デバイスの **患者の状態** を変更する。

:::image type="content" source="media/lamna-in-patient.png" alt-text="入院患者の状態":::

また、 **[Go to remote patient dashboard]\(遠隔患者のダッシュボードに移動する\)** を選択して、Burkville 病院のオペレーター ダッシュボードを表示することもできます。 このダッシュボードには、類似した一連のアクション、テレメトリ、および情報が含まれています。 さらに、使用されている複数のデバイスを表示し、それぞれの **ファームウェアを更新** することもできます。

:::image type="content" source="media/lamna-remote.png" alt-text="リモート オペレーター ダッシュボード":::

### <a name="device-templates"></a>デバイス テンプレート

**[デバイス テンプレート]** を選択した場合、デバイスの種類が 2 つテンプレートに表示されます。

* **Smart Vitals Patch**:このデバイスは、各種の生存徴候を測定する修正プログラムを表します。 病院の内外の患者を監視するために使用します。 テンプレートを選択すると、パッチがバッテリ レベルやデバイス温度などのデバイス データを送信するだけでなく、呼吸数や血圧などの患者の健康データも送信していることを確認できます。

* **Smart Knee Brace**:このデバイスは、患者が膝関節置換術から回復する際に使用する膝関節支持帯を表します。 このテンプレートを選択すると、デバイス データ、関節可動域、加速などの能力が表示されます。

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Smart patch テンプレート":::

### <a name="device-groups"></a>Device groups

デバイス グループを使用すると、一連のデバイスを論理的にグループ化して、それらに対してクエリや操作を一括で実行できます。

[デバイス グループ] タブを選択すると、アプリケーションのデバイス テンプレートごとに既定のデバイス グループが表示されます。 また、"**Provision devices (デバイスのプロビジョニング)** " および "**Devices with outdated firmware (古いファームウェアがインストールされたデバイス)** " という 2 つの追加のサンプル デバイス グループが作成されています。 これらのサンプル デバイス グループを入力に使用して、いくつかの[ジョブ](#jobs)をアプリケーションで実行することができます。

### <a name="rules"></a>ルール

**[Rules]\(ルール\)** を選択すると、次の 3 つのルールがテンプレートに表示されます。

* **Brace temperature high (関節支持帯の温度が高い)** :このルールは、Smart Knee Brace のデバイス温度について、95&deg;F を超える状態が 5 分を超えたときトリガーされます。 このルールを使用して、患者とケア チームにアラートを出し、遠隔操作でデバイスを冷却します。

* **Fall detected (転倒を検出)** :このルールは、患者の転倒が検出されるとトリガーされます。 このルールを使用して、転倒した患者を支援するためのオペレーション チームを配置するアクションを構成します。

* **Patch battery low (パッチのバッテリーが低下している)** :このルールは、デバイスのバッテリ レベルが 10% を下回ったときにトリガーされます。 このルールを使用して、デバイスの充電のための患者への通知をトリガーします。

:::image type="content" source="media/brace-temp-rule.png" alt-text="ルール":::

### <a name="jobs"></a>ジョブ

ジョブを使用すると、[デバイス グループ](#device-groups)を入力として使用して、一連のデバイスに対して一括操作を実行できます。 このアプリケーション テンプレートには、オペレーターが実行できる 2 つのサンプル ジョブがあります。

* **Update knee brace firmware (膝関節支持帯のファームウェアを更新する)** : このジョブでは、**Devices with outdated firmware (古いファームウェアがインストールされたデバイス)** デバイス グループのデバイスを検出し、最新ファームウェア バージョンに更新するコマンドを実行します。 このサンプル ジョブは、デバイスが、**更新** コマンドを処理してクラウドからファームウェア ファイルをフェッチできることを前提としています。  

* **Re-provision devices (デバイスを再プロビジョニングする)** : 最近病院に返されてきた一連のデバイスがあるとします。 このジョブは、**Provision devices (デバイスのプロビジョニング)** というデバイス グループからデバイスを検索し、次の一連の患者用にプロビジョニングします。

### <a name="devices"></a>デバイス

**[デバイス]** タブを選択し、**Smart Knee Brace** のインスタンスを選択します。 選択した特定のデバイスに関する情報を調べることができるビューが 3 つあります。 これらのビューは、デバイスのデバイス テンプレートを作成するときに作成されて発行されます。 そのため、これらのビューは、接続またはシミュレートするすべてのデバイスで一貫しています。

**[Dashboard]\(ダッシュボード\)** ビューには、オペレーター向けに、デバイスのテレメトリとプロパティの概要が表示されます。

**[Properties]\(プロパティ\)** タブでは、クラウド プロパティの編集やデバイス プロパティの読み取りと書き込みを行うことができます。

**[コマンド]** タブでは、デバイスに対してコマンドを実行できます。

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="膝関節支持帯のダッシュボード":::

### <a name="data-export"></a>データのエクスポート

データのエクスポートを使用すると、デバイス データを、[Azure API for FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir) などの他の Azure サービスに継続的にエクスポートできます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリケーションをもう使わない場合は、 **[管理] > [アプリケーションの設定]** の順に選択し、 **[削除]** をクリックして、アプリケーションを削除します。

:::image type="content" source="media/admin-delete.png" alt-text="リソースをクリーンアップする":::

## <a name="next-steps"></a>次のステップ

次の記事に進んで、IoT Central アプリケーションに接続するプロバイダー ダッシュボードを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [プロバイダー ダッシュボードを構築する](tutorial-health-data-triage.md)