---
title: Azure DevTest Labs でのパブリックな環境の構成と使用 | Microsoft Docs
description: Azure DevTest Labs でパブリックな環境を構成して使用する方法を説明します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2018
ms.author: spelluru
ms.openlocfilehash: d93818cd875c4050b1b35f21ce580933776c5bc5
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234778"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Azure DevTest Labs でのパブリックな環境の構成と使用
Azure DevTest Labs には、[Azure Resource Manager テンプレートのパブリック リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Environments)が装備されています。このテンプレートを使用すると、自分で外部 GitHub ソースに接続しなくても、環境を作成できます。 このリポジトリには、Azure Web Apps、Service Fabric クラスター、開発の SharePoint ファーム環境など、頻繁に使用されるテンプレートが含まれています。 この機能は、作成するすべてのラボに含まれている成果物のパブリック リポジトリに似ています。 環境リポジトリを使用すると、事前に作成されている環境テンプレートに最低限の入力パラメーターを指定して、すばやく作業を開始できます。こうして、ラボ内の PaaS リソースでスムーズに作業を開始できます。 

## <a name="configuring-public-environments"></a>パブリックな環境の構成
ラボ所有者は、ラボの作成時に、ラボ用のパブリックな環境リポジトリを有効にできます。 ラボ用のパブリックな環境を有効にするには、ラボの作成時に、**[パブリックな環境]** フィールドで **[On]** を選択します。 

![新しいラボのパブリックな環境の有効化](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


既存のラボでは、パブリックな環境リポジトリは有効になっていません。 リポジトリ内のテンプレートを使用するには、手動で有効にします。 Resource Manager テンプレートを使用して作成されたラボの場合も、リポジトリは既定で無効になっています。

ラボでパブリックな環境を有効/無効にすることも、次の手順を使用してラボのユーザーが特定の環境のみを使用できるようにすることもできます。 

1. ラボの **[構成とポリシー]** を選択します。 
2. **[仮想マシンのベース]** セクションで、**[パブリックな環境]** を選択します。
3. ラボでパブリックな環境を有効にするには、**[はい]** を選択します。 それ以外の場合は、**[いいえ]** を選択します。 
4. パブリックな環境を有効にした場合は、既定でリポジトリ内のすべての環境が有効になります。 環境を選択解除すると、その環境をラボ ユーザーが使用できないようにすることができます。 

![パブリックな環境のページ](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>ラボ ユーザーによる環境テンプレートの使用
ラボ ユーザーは、ラボ ページのツール バーから **[+ 追加]** を選択するだけで、有効な環境テンプレートのリストから新しい環境を作成できます。 ベースのリストの一番上にあるのは、ラボ管理者によって有効にされたパブリックな環境のテンプレートです。

![パブリックな環境のテンプレート](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>次の手順
このリポジトリは、投稿して、頻繁に使用される便利な Resource Manager テンプレートを自分で追加できるオープン ソースのリポジトリです。 リポジトリに対して pull request を送信するだけで、投稿できます。  
