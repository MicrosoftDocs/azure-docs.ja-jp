---
title: ロールに対する既定の TEMP フォルダーのサイズが小さすぎる | Microsoft Docs
description: クラウド サービス ロールでは、TEMP フォルダー用の領域量が限られています。 この記事では、領域不足の回避方法に関する推奨事項をいくつか示します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 1b7bfb47168c31f9e2e1b7e40764439118c00805
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743204"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-classic-webworker-role"></a>クラウド サービス (クラシック) の Web/worker ロールに対する既定のTEMP フォルダーのサイズが小さすぎる

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

クラウド サービスの worker または Web ロールの既定の一時ディレクトリの最大サイズは 100 MB ですが、ある時点でいっぱいになる可能性があります。 この記事では、一時ディレクトリの領域不足を回避する方法について説明します。

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>領域が不足する理由
標準的な Windows 環境変数 TEMP と TMP は、アプリケーションで実行されているコードで使用可能です。 TEMP と TMP は両方とも、最大サイズが 100 MB の 1 つのディレクトリを指します。 このディレクトリに格納されているデータは、クラウド サービスのライフサイクル全体で保持されません。クラウド サービスのロール インスタンスがリサイクルされると、ディレクトリはクリーニングされます。

## <a name="suggestion-to-fix-the-problem"></a>問題の修正に関する推奨事項
次のいずれかの代替手段を実装します。

* ローカル ストレージ リソースを構成し、TEMP や TMP を使用せずに直接アクセスします。 アプリケーション内で実行されているコードからローカル ストレージ リソースにアクセスするには、 [RoleEnvironment.GetLocalResource](/previous-versions/azure/reference/ee772845(v=azure.100)) メソッドを呼び出します。
* ローカル ストレージ リソースを構成し、TEMP と TMP ディレクトリがローカル ストレージ リソースのパスを指すように指定します。 この変更は [RoleEntryPoint.OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) メソッド内で実行する必要があります。

次のコード例では、OnStart メソッド内から TEMP および TMP のターゲット ディレクトリを変更する方法を示します。

```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>次のステップ
ブログ「 [How to increase the size of the Azure Web Role ASP.NET Temporary Folder (Azure Web ロール ASP.NET の一時フォルダーのサイズを増やす方法)](/archive/blogs/kwill/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder)」の説明をご覧ください。

クラウド サービスの他の [トラブルシューティングに関する記事](/visualstudio/azure/vs-azure-tools-debugging-cloud-services-overview) を参照します。

Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする方法については、 [Kevin Williamson によるブログ シリーズ](/archive/blogs/kwill/windows-azure-paas-compute-diagnostics-data)をご覧ください。