---
title: ロールに対する既定の TEMP フォルダーのサイズが小さすぎる | Microsoft Docs
description: クラウド サービス ロールでは、TEMP フォルダー用の領域量が限られています。 この記事では、領域不足の回避方法に関する推奨事項をいくつか示します。
services: cloud-services
documentationcenter: ''
author: simonxjx
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 9f2af8dd-2012-4b36-9dd5-19bf6a67e47d
ms.service: cloud-services
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 06/15/2018
ms.author: v-six
ms.openlocfilehash: 0b869b73a79872d9263058bedfead018e18721c1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154995"
---
# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>クラウド サービスの Web/worker ロールに対する既定の一時フォルダーのサイズが小さすぎる
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

## <a name="next-steps"></a>次の手順
ブログ「 [How to increase the size of the Azure Web Role ASP.NET Temporary Folder (Azure Web ロール ASP.NET の一時フォルダーのサイズを増やす方法)](https://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx)」の説明をご覧ください。

クラウド サービスの他の [トラブルシューティングに関する記事](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/vs-azure-tools-debugging-cloud-services-overview.md) を参照します。

Azure PaaS コンピューターの診断データを使用してクラウド サービス ロールの問題をトラブルシューティングする方法については、 [Kevin Williamson によるブログ シリーズ](https://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx)をご覧ください。
