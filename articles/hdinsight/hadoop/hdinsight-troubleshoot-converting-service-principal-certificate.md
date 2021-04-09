---
title: 証明書の内容を base-64 に変換する - Azure HDInsight
description: Azure HDInsight でサービス プリンシパル証明書の内容を base-64 でエンコードされた文字列形式に変換する
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/31/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 0ee2e783a7a9443db4c96817cf611272667c675a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944403"
---
# <a name="converting-service-principal-certificate-contents-to-base-64-encoded-string-format-in-hdinsight"></a>HDInsight でサービス プリンシパル証明書の内容を base-64 でエンコードされた文字列形式に変換する

この記事では、Azure HDInsight クラスターと対話するときの問題のトラブルシューティング手順と可能な解決策について説明します。

## <a name="issue"></a>問題

base-64 以外の文字、3 個以上のパディング文字、またはパディング文字の間の空白以外の文字が含まれているため、入力が有効な base-64 文字列ではないことを示すエラー メッセージが表示されます。

## <a name="cause"></a>原因

PowerShell または Azure テンプレートのデプロイを使用して、プライマリまたは追加のストレージとして Data Lake を使用するクラスターを作成する場合、Data Lake ストレージ アカウントにアクセスするために提供されるサービス プリンシパルの証明書の内容は、base-64 形式になっています。 pfx 証明書の内容を base-64 でエンコードされた文字列に不適切に変換すると、このエラーが発生する可能性があります。

## <a name="resolution"></a>解決方法

pfx 形式のサービス プリンシパル証明書を用意したら (サンプルのサービス プリンシパル作成手順については[こちら](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage)を参照してください)、次の PowerShell コマンドまたは C# スニペットを使用して、証明書の内容を base-64 形式に変換します。

```powershell
$servicePrincipalCertificateBase64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes(path-to-servicePrincipalCertificatePfxFile))
```

```csharp
using System;
using System.IO;

namespace ConsoleApplication
{
    class Program
    {
        static void Main(string[] args)
        {
            var certContents = File.ReadAllBytes(@"<path to pfx file>");
            string certificateData = Convert.ToBase64String(certContents);
            System.Diagnostics.Debug.WriteLine(certificateData);
        }
    }
}
```

## <a name="next-steps"></a>次のステップ

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]