---
title: "Application Gateway と Azure Security Center の統合 | Microsoft Docs"
description: "このページでは、Application Gateway を Azure Security Center に統合する方法に関する情報を示します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 737cdff3140be68cf9d6d396b470dd09c65c52f2
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017


---

# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Application Gateway と Azure Security Center の統合の概要

Application Gateway と Security Center を使用して Web アプリケーション リソースを保護する方法について説明します。 Application Gateway Web アプリケーション ファイアウォール (WAF) を [Security Center](../security-center/security-center-intro.md) と統合すると、環境内で保護されていない Web アプリケーションに対する脅威の防止、検出、およびそれらへの対応を行うためのシームレスなビューが提供されます。

## <a name="overview"></a>概要

Application Gateway WAF は、Web アプリケーションの悪用や脆弱性を防ぐための Security Center における推奨事項です。 WAF で保護されていない Web 対応のリソースは、重大度の高い推奨事項として Security Center に表示されます。 Web アプリケーション ファイアウォールに関する推奨事項は、**[概要]** ページの **[アプリケーション]** に表示されます。

![Security Center との統合][1]

Web アプリケーション ファイアウォールに関するいずれかの推奨事項をクリックすると、その詳細を示す新しいブレードが開きます。

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>既存のリソースに Web アプリケーション ファイアウォールを追加する

**[その他のサービス]** > **[セキュリティ + ID]** > **[Security Center]** に移動し、**[Security Center - 概要]** ブレードの **[アプリケーション]** をクリックします。 **[Security Center - アプリケーション]** ブレードのテーブルには、Security Center がサブスクリプションで検出したアプリケーションの一覧が含まれています。

![Web アプリケーション][3]

重大な問題を含む Web アプリケーションをクリックすると、**[アプリケーションのセキュリティ正常性]** ブレードが表示されます。 次の図では、Web アプリケーションが Web アプリケーション ファイアウォールによって保護されていません。 

![保護されていない Web リソース][2]

**[推奨事項]** の下にある **[Web アプリケーション ファイアウォールを追加する]** をクリックして、**[Add a Web Application Firewall]\(Web アプリケーション ファイアウォールの追加\)** ブレードを開きます。

既存の Application Gateway がない場合や、新しい Application Gateway を作成する場合は、**[新規作成]** をクリックし、**[Create a new Web Application Firewall]\(新しい Web アプリケーション ファイアウォールの作成\)** ブレードの **[Microsoft - Application Gateway]** をクリックします。 これにより、Application Gateway を作成する手順が表示されます。 ここで、Web アプリケーションが保護対象のリソースとして追加され、Security Center はこのリソースが Web アプリケーション ファイアウォールによって保護されていることを追跡します。 これにより、リソースがバックエンド プールのメンバーとして追加されることはありません。

既存の Application Gateway がある場合は、**[既存のソリューションの使用]** で選択できます。

![Web アプリケーション ファイアウォールの追加ブレード][4]

Security Center を通じて Web アプリケーションを Application Gateway に追加しても、リソースがバックエンド プールのメンバーとして追加されることはありません。この操作は、Application Gateway のリソースで直接行う必要があります。

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>既存の Web アプリケーション ファイアウォールにリソースを追加する

**[その他のサービス]** > **[セキュリティ + ID]** > **[Security Center]** に移動し、**[Security Center - 概要]** ブレードの **[パートナー ソリューション]** をクリックします。 Security Center 対応の既存の Application Gateway が **[パートナー ソリューション]** ブレードに表示されます。

![パートナー ソリューション][7]

**[アプリをリンク]** をクリックして、**[アプリケーションのリンク]** ブレードを開きます。このブレードには、既存のアプリケーションを選択するためのオプションがあります。 保護するアプリケーションを選択し、**[OK]** をクリックします。 これにより、Application Gateway のバックエンド プールに Web アプリケーションが追加されることはありません。 Security Center で追跡できるように、リソースが保護対象のリソースとして設定されます。 リソースをバックエンド プールのメンバーとして追加する操作は Application Gateway で行う必要があります。現在のブレードの **[コンソール]** をクリックすると、Application Gateway のリソースが表示され、バックエンド プールに Web アプリケーションを追加できます。

![パートナー ソリューションのアプリケーション][6]

## <a name="finalize-configuration"></a>構成を完了する

Security Center は、保護対象のリソースとして Application Gateway に追加されたアプリケーションを追跡します。  また、このリソースの正常性を監視し、Application Gateway によって保護されていることを確認します。 次の手順では、Application Gateway のバックエンド プールに仮想マシンのプライベート IP、パブリック IP、または NIC を追加します。 リソースが追加されるまで、追加の推奨事項である **[アプリケーションの保護を完了する]** が表示されます。

![Web アプリケーション ファイアウォールの追加ブレード][5]

## <a name="security-alerts"></a>セキュリティ アラート

Security Center の **[検出]** > **[セキュリティ アラート]** に移動します。  ここには、Application Gateway の WAF アラートが表示されます。 アラートは、WAF ルールによって分類されます。

![セキュリティ アラート][8]

ルールをクリックすると、該当する WAF ルールのアラートの一覧が表示されます。 各アラートには、追加の詳細が示されます。 この詳細には、Application Gateway へのリンクが含まれています。
 
![アラートの詳細][9]

## <a name="next-steps"></a>次のステップ

既存の Application Gateway で Web アプリケーション ファイアウォールを有効にする方法については、「[既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する](application-gateway-web-application-firewall-portal.md#add-web-application-firewall-to-an-existing-application-gateway)」を参照してください。

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png
