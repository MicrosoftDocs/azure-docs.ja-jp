---
title: "Azure Cloud Services のデバッグのオプション | Microsoft Docs"
description: "Azure Cloud Services のデバッグ"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 80755da7-8350-4f5c-97ce-2962beabb36d
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/18/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: e79aa1db6c89ad1e382c7a70017f868fe186aa74
ms.lasthandoff: 03/22/2017


---
# <a name="learn-the-various-ways-to-debug-an-azure-cloud-service"></a>Azure クラウド サービスをデバッグするさまざまな方法
この記事では、Azure クラウド サービスをデバッグするさまざまな方法へのリンクを提供します。 

## <a name="debugging-an-azure-cloud-service-in-visual-studio"></a>Visual Studio で Azure クラウド サービスをデバッグする
Azure コンピューティング エミュレーターを使用してローカル コンピューターでクラウド サービスをデバッグすれば、時間とコストの節約になります。 サービスをデプロイする前にローカルでデバッグすると、コンピューティング時間の料金を支払うことなく、信頼性とパフォーマンスを改善できます。 ただし、一部には Azure でクラウド サービスを実行した場合にのみ発生するエラーもあります。 Azure でクラウド サービスを実行するときにのみ発生するエラーは、サービスの発行時にリモート デバッグを有効にしてから、ロール インスタンスにデバッガーをアタッチすることでデバッグできます。 詳細については、「 [ローカル コンピューターでクラウド サービスをデバッグする](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-your-cloud-service-on-your-local-computer)」を参照してください。

## <a name="using-azure-diagnostics"></a>Azure 診断を使用する 
Azure 診断を使用すると、ロールが開発環境または Azure のどちらで実行されているかにかかわらず、ロール内で実行されているコードから詳細な情報をログに記録できます。 詳細については、「 [Azure Cloud Services での Azure 診断の有効化](http://go.microsoft.com/fwlink/p/?LinkId=400450)」を参照してください。

## <a name="using-intellitrace"></a>IntelliTrace を使用する 
Visual Studio Enterprise を使用して .NET Framework 4.5 を対象とするロールを作成する場合は、Visual Studio から Azure クラウド サービスをデプロイする時点で IntelliTrace を有効にすることができます。 IntelliTrace が提供するログを使用すると、Azure で実行しているかのようにアプリケーションを Visual Studio でデバッグすることができます。 詳細については、「 [IntelliTrace および Visual Studio を使用した発行済みのクラウド サービスのデバッグ](http://go.microsoft.com/fwlink/p/?LinkId=623016)」を参照してください。

## <a name="remote-debugging"></a>リモート デバッグ 
Visual Studio からクラウド サービスをデプロイするときに、そのリモート デバッグを有効にできます。 デプロイメントのリモート デバッグを有効にすると、各ロール インスタンスを実行する仮想マシンにリモート デバッグ サービスがインストールされます。 `msvsmon.exe` をはじめとするこれらのサービスは、パフォーマンスに影響せず、追加コストも発生しません。 詳細については、「 [Azure でクラウド サービスをデバッグする](vs-azure-tools-debug-cloud-services-virtual-machines.md#debug-a-cloud-service-in-azure)」を参照してください。

## <a name="next-steps"></a>次のステップ
- [Visual Studio での Azure クラウド サービスまたは仮想マシンのデバッグ](./vs-azure-tools-debug-cloud-services-virtual-machines.md) - Azure クラウド サービスをデバッグする方法の詳細を説明します。
