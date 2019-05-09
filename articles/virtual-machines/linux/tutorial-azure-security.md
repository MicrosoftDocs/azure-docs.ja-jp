---
title: チュートリアル - Azure 内の Linux VM に Azure Security Center を使用する | Microsoft Docs
description: このチュートリアルでは、Azure 内の Linux 仮想マシンをセキュリティで保護するために役立つ Azure Security Center の機能について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/11/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: b4c484f3dd118a19291741b071432b09894e836c
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2019
ms.locfileid: "56593303"
---
# <a name="tutorial-use-azure-security-center-to-monitor-linux-virtual-machines"></a>チュートリアル:Azure Security Center を使用して Linux 仮想マシンを監視する

Azure リソースのセキュリティの状態は、Azure Security Center で可視化することができます。 Security Center には、包括的なセキュリティ監視機能が備わっています。 他の方法では見過ごされてしまう可能性のある脅威も検出することが可能です。 このチュートリアルでは、Azure Security Center と次の方法について説明します。
 
> [!div class="checklist"]
> * データ収集を設定する
> * セキュリティ ポリシーを設定する
> * 構成の正常性に関する問題を確認して解決する
> * 検出された脅威を確認する

## <a name="security-center-overview"></a>Security Center の概要

Security Center は、仮想マシン (VM) の構成で問題となりうる箇所や、標的となるセキュリティ面の脅威を特定します。 たとえばネットワーク セキュリティ グループが関連付けられていない VM や、暗号化されていないディスク、リモート デスクトップ プロトコル (RDP) でのブルート フォース攻撃を特定することができます。 このような情報が、Security Center のダッシュボードに、グラフを使って見やすく表示されます。

Azure ポータルから Security Center ダッシュボードにアクセスするには、メニューから **[Security Center]** を選択します。 ダッシュボードでは、Azure 環境のセキュリティの正常性、現在の推奨事項の数、脅威アラートの現在の状態を確認できます。 大まかな情報が表示されているそれぞれのグラフを展開すると、さらに詳しい情報が表示されます。

![Security Center ダッシュボード](./media/tutorial-azure-security/asc-dash.png)

Security Center の機能はデータを検出するだけではありません。検出された問題についての推奨事項が提供されます。 たとえばネットワーク セキュリティ グループが関連付けられていない状態で VM がデプロイされていた場合、Security Center によって推奨事項と修復手順が表示されます。 修復は自動化されており、Security Center を離れる必要はありません。  

![Recommendations](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>データ収集を設定する

VM のセキュリティ構成を可視化するためには、まず Security Center のデータ収集を設定する必要があります。 これには、データ収集をオンにすることが含まれます。これにより、ご利用のサブスクリプションのすべての VM に Microsoft Monitoring Agent が自動的にインストールされます。

1. Security Center ダッシュボードで、**[セキュリティ ポリシー]** をクリックしてサブスクリプションを選択します。 
2. **[データ収集]** の **[自動プロビジョニング]** で **[オン]** を選択します。
3. **[既定のワークスペース構成]** を **[Security Center によって作成されたワークスペースを使用 (既定)]** のままにします。
4. **[セキュリティ イベント]** で、既定のオプションの **[共通]** を保持します。
4. ページの上部にある **[保存]** をクリックします。 

これですべての VM に Security Center のデータ収集エージェントがインストールされ、データの収集が開始されます。 

## <a name="set-up-a-security-policy"></a>セキュリティ ポリシーを設定する

Security Center によるデータの収集と推奨事項の提示の対象となる項目は、セキュリティ ポリシーを使って定義します。 Azure リソースのまとまりごとに異なるセキュリティ ポリシーを適用することができます。 既定ではすべてのポリシー項目と照らして Azure リソースが評価されますが、すべての Azure リソースを対象として、または特定のリソース グループを対象として、個々のポリシー項目を無効にすることができます。 Security Center のセキュリティ ポリシーの詳細については、「[Azure Security Center でのセキュリティ ポリシーの設定](../../security-center/security-center-policies.md)」を参照してください。 

サブスクリプション全体を対象とするセキュリティ ポリシーを設定するには、次の手順に従います。

1. Security Center ダッシュボードで、**[セキュリティ ポリシー]** を選択し、ご利用のサブスクリプションを選択します。
2. **[セキュリティ ポリシー]** ブレードで **[セキュリティ ポリシー]** を選択します。 
3. **[セキュリティ ポリシー - セキュリティ ポリシー]** ブレードで、サブスクリプションに適用するポリシー項目をオンまたはオフにします。
4. 設定の選択が完了したら、ブレードの上部にある **[保存]** を選択します。 

![固有のポリシー](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>VM の構成の正常性を表示する

データ収集を有効にしてセキュリティ ポリシーを設定すると、Security Center によるアラートの生成と推奨事項の提示が開始されます。 VM をデプロイすると、データ収集エージェントがインストールされます。 その後、その新しい VM のデータが Security Center に収集されていきます。 VM の構成の正常性の詳細については、[Security Center での VM の保護](../../security-center/security-center-virtual-machine-recommendations.md)に関するページを参照してください。 

データが収集されると、各 VM のリソースの正常性および関連する Azure リソースの正常性が収集されます。 その情報が見やすいグラフの形で表示されます。 

リソースの正常性を表示するには、次の手順に従います。

1.  Security Center ダッシュボードの **[防止]** で、**[コンピューティング]** を選択します。 
2.  **[コンピューティング]** ブレードで **[VM とコンピューター]** を選択します。 このビューでは、自分の VM すべてについて構成の状態の概要を確認できます。

![コンピューティングの正常性](./media/tutorial-azure-security/compute-health.png)

特定の VM に対するすべての推奨事項を表示するには、対象の VM を選択します。 

## <a name="remediate-configuration-issues"></a>構成の問題を修復する

Security Center による構成データの収集が開始されると、設定したセキュリティ ポリシーに基づいて推奨事項が提示されます。 たとえば、ネットワーク セキュリティ グループが関連付けられていない状態で VM を設定していた場合には、ネットワーク セキュリティ グループの作成を促す内容の推奨事項が提示されます。 

推奨事項の一覧を表示する方法は、次のとおりです。 

1. Security Center ダッシュボードで **[推奨事項]** を選択します。
2. 特定の推奨事項を選択します。 その推奨事項が該当する全リソースの一覧が表示されます。
3. 推奨事項を適用するには、リソースを選択します。 
4. 修復手順に従います。 

多くの場合、Security Center を離れることなくその場で問題を修復するための手順が表示されます。 以下の例では、ネットワーク セキュリティ グループに割り当てられている受信の規則が無制限になっていることが、Security Center によって検出されています。 推奨事項ページで **[受信の規則を編集する]** ボタンを選択できます。 規則を編集するために必要な UI が表示されます。 

![Recommendations](./media/tutorial-azure-security/remediation.png)

推奨事項の内容に従って問題を修復すると、解決済みとしてマークされます。 

## <a name="view-detected-threats"></a>検出された脅威を表示する

Security Center には、リソースの構成に関する推奨事項を提示するだけでなく、脅威の検出に関するアラートを表示する機能が備わっています。 このセキュリティ アラート機能は、各 VM、Azure のネットワーク ログ、および接続されているパートナー ソリューションから収集されるデータを集計し、Azure リソースに対するセキュリティ面の脅威を検出するものです。 Security Center の脅威検出機能の詳細については、「[Azure Security Center の検出機能](../../security-center/security-center-detection-capabilities.md)」を参照してください。

セキュリティ アラート機能を使用するには、Security Center の価格レベルを *Free* から *Standard* に変更する必要があります。 この価格レベルに引き上げるときは、**無料試用版**をご利用いただけます。 

価格レベルを変更する方法は、次のとおりです。  

1. Security Center ダッシュボードで、**[セキュリティ ポリシー]** をクリックしてサブスクリプションを選択します。
2. **[価格レベル]** を選択します。
3. **[Standard]** を選択し、ブレードの上部にある **[保存]** をクリックします。


価格レベルを変更すると、セキュリティ面の脅威が検出されるたびにセキュリティ アラートのグラフにデータが反映されるようになります。

![セキュリティのアラート](./media/tutorial-azure-security/security-alerts.png)

アラートを選択すると情報が表示されます。 たとえば脅威の詳細、検出時点、脅威の試行回数、推奨される修復方法などの情報が表示されます。 次の例では、RDP 経由のブルート フォース攻撃が検出されており、試行に 294 回失敗したことがわかります。 また、推奨される解決方法が表示されています。

![RDP 攻撃](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>次の手順
このチュートリアルでは、Azure Security Center を設定した後、Security Center で VM を確認しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * データ収集を設定する
> * セキュリティ ポリシーを設定する
> * 構成の正常性に関する問題を確認して解決する
> * 検出された脅威を確認する

次のチュートリアルに進み、Jenkins、GitHub、Docker を使った CI/CD パイプラインの作成について理解を深めてください。

> [!div class="nextstepaction"]
> [Jenkins、GitHub、Docker を使用した CI/CD インフラストラクチャの作成](tutorial-jenkins-github-docker-cicd.md)

