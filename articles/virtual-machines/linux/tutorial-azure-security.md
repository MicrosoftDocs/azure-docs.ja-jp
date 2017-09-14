---
title: "Azure の Linux 仮想マシンと Azure Security Center | Microsoft Docs"
description: "Azure Security Center による Azure Linux 仮想マシンのセキュリティについて説明します。"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/07/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: 222cb9629e50e49ce08e0737d7f2570e9187317a
ms.contentlocale: ja-jp
ms.lasthandoff: 05/17/2017

---
# <a name="monitor-virtual-machine-security-by-using-azure-security-center"></a>Azure Security Center を使った仮想マシン セキュリティの監視

Azure リソースのセキュリティの状態は、Azure Security Center で可視化することができます。 Security Center には、包括的なセキュリティ監視機能が備わっています。 他の方法では見過ごされてしまう可能性のある脅威も検出することが可能です。 このチュートリアルでは、Azure Security Center と次の方法について説明します。
 
> [!div class="checklist"]
> * データ収集を設定する
> * セキュリティ ポリシーを設定する
> * 構成の正常性に関する問題を確認して解決する
> * 検出された脅威を確認する  

## <a name="security-center-overview"></a>Security Center の概要

Security Center は、仮想マシン (VM) の構成で問題となりうる箇所や、標的となるセキュリティ面の脅威を特定します。 たとえばネットワーク セキュリティ グループが関連付けられていない VM や、暗号化されていないディスク、リモート デスクトップ プロトコル (RDP) でのブルート フォース攻撃を特定することができます。 このような情報が、Security Center のダッシュボードに、グラフを使って見やすく表示されます。

Azure Portal から Security Center のダッシュボードにアクセスするには、メニューから **[Security Center]** を選択します。 ダッシュボードでは、Azure 環境のセキュリティの正常性、現在の推奨事項の数、脅威アラートの現在の状態を確認できます。 大まかな情報が表示されているそれぞれのグラフを展開すると、さらに詳しい情報が表示されます。

![Security Center ダッシュボード](./media/tutorial-azure-security/asc-dash.png)

Security Center の機能はデータを検出するだけではありません。検出された問題についての推奨事項が提供されます。 たとえばネットワーク セキュリティ グループが関連付けられていない状態で VM がデプロイされていた場合、Security Center によって推奨事項と修復手順が表示されます。 修復は自動化されており、Security Center を離れる必要はありません。  

![推奨事項](./media/tutorial-azure-security/recommendations.png)

## <a name="set-up-data-collection"></a>データ収集を設定する

VM のセキュリティ構成を可視化するためには、まず Security Center のデータ収集を設定する必要があります。 この作業には、データ収集を有効にすることや、収集したデータを保持するための Azure ストレージ アカウントを作成することが含まれます。 

1. Security Center のダッシュボードで、**[セキュリティ ポリシー]** をクリックしてサブスクリプションを選択します。 
2. **[データ収集]** で **[オン]** を選択します。
3. ストレージ アカウントを作成するには、**[ストレージ アカウントの選択]** を選択し、 **[OK]** をクリックします。
4. **[セキュリティ ポリシー]** ブレードで **[保存]** を選択します。 

これですべての VM に Security Center のデータ収集エージェントがインストールされ、データの収集が開始されます。 

## <a name="set-up-a-security-policy"></a>セキュリティ ポリシーを設定する

Security Center によるデータの収集と推奨事項の提示の対象となる項目は、セキュリティ ポリシーを使って定義します。 Azure リソースのまとまりごとに異なるセキュリティ ポリシーを適用することができます。 既定ではすべてのポリシー項目と照らして Azure リソースが評価されますが、すべての Azure リソースを対象として、または特定のリソース グループを対象として、個々のポリシー項目を無効にすることができます。 Security Center のセキュリティ ポリシーの詳細については、「[Azure Security Center でのセキュリティ ポリシーの設定](../../security-center/security-center-policies.md)」を参照してください。 

すべての Azure リソースを対象にセキュリティ ポリシーを設定するには、次の手順に従います。

1. Security Center のダッシュボードで、**[セキュリティ ポリシー]** を選択してサブスクリプションを選択します。
2. **[Prevention policy]\(防止ポリシー\)** を選択します。
3. すべての Azure リソースに適用するポリシー項目をオンまたはオフにします。
4. 必要な設定を選択したら、**[OK]** を選択します。
5. **[セキュリティ ポリシー]** ブレードで **[保存]** を選択します。 

特定のリソース グループを対象にポリシーを設定するには、次の手順に従います。

1. Security Center のダッシュボードで、**[セキュリティ ポリシー]** を選択してリソース グループを選択します。
2. **[Prevention policy]\(防止ポリシー\)** を選択します。
3. 対象のリソース グループに適用するポリシー項目をオンまたはオフにします。
4. **[継承]** の **[固有]** を選択します。
5. 必要な設定を選択したら、**[OK]** を選択します。
6. **[セキュリティ ポリシー]** ブレードで **[保存]** を選択します。  

特定のリソース グループを対象とするデータ収集のオンとオフは、このページで切り替えることもできます。

次の例では、*myResoureGroup* という名前のリソース グループを対象に固有のポリシーを作成しています。 このポリシーでは、ディスクの暗号化と Web アプリケーション ファイアウォールに関する推奨事項の提示がオフになっています。

![固有のポリシー](./media/tutorial-azure-security/unique-policy.png)

## <a name="view-vm-configuration-health"></a>VM の構成の正常性を表示する

データ収集を有効にしてセキュリティ ポリシーを設定すると、Security Center によるアラートの生成と推奨事項の提示が開始されます。 VM をデプロイすると、データ収集エージェントがインストールされます。 その後、その新しい VM のデータが Security Center に収集されていきます。 VM の構成の正常性の詳細については、[Security Center での VM の保護](../../security-center/security-center-virtual-machine-recommendations.md)に関するページを参照してください。 

データが収集されると、各 VM のリソースの正常性および関連する Azure リソースの正常性が集計されます。 その情報が見やすいグラフの形で表示されます。 

リソースの正常性を表示するには、次の手順に従います。

1.  Security Center ダッシュボードの **[Resource security health]\(リソースのセキュリティの正常性\)** で、**[コンピューティング]** を選択します。 
2.  **[コンピューティング]** ブレードで **[仮想マシン]** を選択します。 このビューでは、自分の VM すべてについて構成の状態の概要を確認できます。

![コンピューティングの正常性](./media/tutorial-azure-security/compute-health.png)

特定の VM に対するすべての推奨事項を表示するには、対象の VM を選択します。 推奨事項と修復については、このチュートリアルの次のセクションで詳しく説明します。

## <a name="remediate-configuration-issues"></a>構成の問題を修復する

Security Center による構成データの収集が開始されると、設定したセキュリティ ポリシーに基づいて推奨事項が提示されます。 たとえば、ネットワーク セキュリティ グループが関連付けられていない状態で VM を設定していた場合には、ネットワーク セキュリティ グループの作成を促す内容の推奨事項が提示されます。 

推奨事項の一覧を表示する方法は、次のとおりです。 

1. Security Center ダッシュボードで **[推奨事項]** を選択します。
2. 特定の推奨事項を選択します。 その推奨事項が該当する全リソースの一覧が表示されます。
3. いずれかの推奨事項を適用するには、特定のリソースを選択します。 
4. 修復手順に従います。 

Security Center では多くの場合、Security Center を離れることなくその場で問題を修復するための手順が表示されます。 以下の例では、ネットワーク セキュリティ グループに割り当てられている受信の規則が無制限になっていることが、Security Center によって検出されています。 推奨事項ページで **[編集]** ボタンを選択してください。 この規則を編集するために必要な画面が表示されます。 

![推奨事項](./media/tutorial-azure-security/remediation.png)

推奨事項の内容に従って問題を修復すると、解決済みとしてマークされます。 

## <a name="view-detected-threats"></a>検出された脅威を表示する

Security Center には、リソースの構成に関する推奨事項を提示するだけでなく、脅威の検出に関するアラートを表示する機能が備わっています。 このセキュリティ アラート機能は、各 VM、Azure のネットワーク ログ、および接続されているパートナー ソリューションから収集されるデータを集計し、Azure リソースに対するセキュリティ面の脅威を検出するものです。 Security Center の脅威検出機能の詳細については、「[Azure Security Center の検出機能](../../security-center/security-center-detection-capabilities.md)」を参照してください。

セキュリティ アラート機能を使用するには、Security Center の価格レベルを *Free* から *Standard* に変更する必要があります。 この価格レベルに引き上げる際は、30 日間の**無料試用版**をご利用いただけます。 

価格レベルを変更する方法は、次のとおりです。  

1. Security Center のダッシュボードで、**[セキュリティ ポリシー]** をクリックしてサブスクリプションを選択します。
2. **[価格レベル]** を選択します。
3. 新しいレベルを選んで **[選択]** を選択します。
4. **[セキュリティ ポリシー]** ブレードで **[保存]** を選択します。 

価格レベルを変更すると、セキュリティ面の脅威が検出されるたびにセキュリティ アラートのグラフにデータが反映されるようになります。

![セキュリティのアラート](./media/tutorial-azure-security/security-alerts.png)

アラートを選択すると情報が表示されます。 たとえば脅威の詳細、検出時点、脅威の試行回数、推奨される修復方法などの情報が表示されます。 この例では、RDP 経由のブルート フォース攻撃が検出されており、試行に 294 回失敗したことがわかります。 また、推奨される解決方法が表示されています。

![RDP 攻撃](./media/tutorial-azure-security/rdp-attack.png)

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Security Center を設定した後、Security Center で VM を確認しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * データ収集を設定する
> * セキュリティ ポリシーを設定する
> * 構成の正常性に関する問題を確認して解決する
> * 検出された脅威を確認する

次のチュートリアルに進み、Jenkins、GitHub、Docker を使った CI/CD パイプラインの作成について理解を深めてください。

> [!div class="nextstepaction"]
> [Jenkins、GitHub、Docker を使用した CI/CD インフラストラクチャの作成](tutorial-jenkins-github-docker-cicd.md)


