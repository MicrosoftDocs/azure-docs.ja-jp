---
title: "テンプレートを使用して Hadoop クラスターを作成する - Azure HDInsight | Microsoft Docs"
description: "Resource Manager テンプレートを使用して HDInsight 用のクラスターを作成する方法について説明します"
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 00a80dea-011f-44f0-92a4-25d09db9d996
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/30/2017
ms.author: jgao
ms.openlocfilehash: 82733e2a3025f932961122bad9d70c26896837b7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-hadoop-clusters-in-hdinsight-by-using-resource-manager-templates"></a>Resource Manager テンプレートを使用して HDInsight に Hadoop クラスターを作成する
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

この記事では、Azure Resource Manager テンプレートを使用して Azure HDInsight クラスターを作成するさまざまな方法について説明します。 詳細については、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)」を参照してください。 その他のクラスター作成ツールと機能を確認するには、このページの上部にあるタブ セレクターをクリックするか、「[クラスターの作成方法](hdinsight-hadoop-provision-linux-clusters.md#cluster-setup-methods)」を参照してください。

## <a name="prerequisites"></a>前提条件
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

この記事の手順に従うには、次のものが必要です。

* [Azure サブスクリプション](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* Azure PowerShell または Azure CLI

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="resource-manager-templates"></a>Resource Manager テンプレート
Resource Manager テンプレートを使用すると、1 つの調整された操作で、アプリケーションのために以下のものを簡単に作成できます。
* HDInsight クラスターとそれらの依存リソース (既定のストレージ アカウントなど)
* その他のリソース (Apache Sqoop を使用する Azure SQL Databaseなど)

テンプレートには、アプリケーションで必要なリソースを定義します。 異なる環境の値を入力するためのデプロイパラメーターも指定します。 テンプレートは、デプロイ用の値を構築するために使用する JSON と式で構成されます。

HDInsight テンプレートのサンプルは、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/?term=hdinsight)」で見つけることができます。 [Resource Manager 拡張機能](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)が付属しているクロスプラットフォームの [Visual Studio Code](https://code.visualstudio.com/#alt-downloads) またはテキスト エディターを使用して、テンプレートをワークステーションのファイルに保存します。 テンプレートを呼び出すさまざまな方法を確認できます。

Resource Manager テンプレートの詳細については、次の記事を参照してください。

* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager テンプレートを使用したアプリケーションのデプロイ](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="generate-templates"></a>テンプレートを生成する

Azure Portal を使用して、クラスターのすべてのプロパティを構成し、デプロイする前にテンプレートを保存できます。 後でテンプレートを再利用できます。

**Azure Portal を使用してテンプレートを生成するには**

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左側のメニューの **[新規]** をクリックし、**[インテリジェンス + 分析]**、**[HDInsight]** の順にクリックします。
3. プロパティを入力する手順に従います。 **[簡易作成]** または**[カスタム]** オプションを使用できます。
4. **[概要]** タブで、**[テンプレートおよびパラメーターをダウンロードする]** をクリックします。

    ![HDInsight Hadoop クラスターでのダウンロード用 Resource Manager テンプレートの作成](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download.png)

    テンプレート ファイル、パラメーター ファイル、およびテンプレートをデプロイするために使用されるコード サンプルが一覧表示されます。

    ![HDInsight Hadoop クラスターでの Resource Manager テンプレートのダウンロード オプション](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-download-options.png)

    ここでは、テンプレートのダウンロード、テンプレート ライブラリへの保存、テンプレートのデプロイを行うことができます。

    ライブラリにあるテンプレートにアクセスするには、左側のメニューで **[その他のサービス]** をクリックし、(**[その他]** カテゴリの下にある) **[テンプレート]** をクリックします。

    > [!Note]
    > テンプレート ファイルとパラメーター ファイルは一緒に使用する必要があります。 そうしない場合、予期しない結果が生じる可能性があります。 たとえば、テンプレートをダウンロードする前に指定した値とは無関係に、**clusterKind** プロパティの既定値が常に **hadoop** になります。



## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

次の手順で、HDInsight に Hadoop クラスターを作成します。

1. 「[付録](#appx-a-arm-template)」の JSON ファイルをワークステーションに保存します。 PowerShell スクリプトでは、ファイル名は `C:\HDITutorials-ARM\hdinsight-arm-template.json` です。
2. 必要に応じてパラメーターと変数を設定します。
3. 次の PowerShell スクリプトを使用して、テンプレートを実行します。

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>"
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and variables
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage account
        $parameters = @{clusterName="$hdinsightClusterName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

    この PowerShell スクリプトは、クラスター名のみを構成します。 ストレージ アカウント名はテンプレートにハードコーディングされています。 クラスター ユーザー パスワードを入力することを求められます  (既定のユーザー名は **admin**です)。SSH ユーザー パスワードを入力することも求められます  (既定の SSH ユーザー名は **sshuser** です)。  

詳細については、「[PowerShell でデプロイする](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template)」を参照してください。

## <a name="deploy-with-cli"></a>CLI を使用したデプロイ
次のサンプルでは、Azure コマンド ライン インターフェイス (CLI) を使用しています。 Resource Manager テンプレートを呼び出すことで、クラスターとその依存ストレージ アカウントとコンテナーを作成します。

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"

次の情報を入力することを求められます。
* クラスター名。
* クラスター ユーザー パスワード。 (既定のユーザー名は **admin**です)。
* SSH ユーザー パスワード  (既定の SSH ユーザー名は **sshuser** です)。

次のコードは、インライン パラメーターを提供します。

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{\"clusterName\":{\"value\":\"hdi1229\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"},\"sshPassword\":{\"value\":\"Pass@word1\"}}'

## <a name="deploy-with-the-rest-api"></a>REST API でデプロイする
「 [REST API でデプロイする](../azure-resource-manager/resource-group-template-deploy-rest.md)」を参照してください。

## <a name="deploy-with-visual-studio"></a>Visual Studio でのデプロイ
 Visual Studio を使用してリソース グループ プロジェクトを作成し、それをユーザー インターフェイスを通して Azure にデプロイします。 プロジェクトに含めるリソースの種類を選択します。 これらのリソースは、Resource Manager テンプレートに自動的に追加されます。 プロジェクトでは、テンプレートをデプロイするための PowerShell スクリプトも提供されます。

Visual Studio とリソース グループの使用の概要については、「 [Visual Studio での Azure リソース グループの作成とデプロイ](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)」を参照してください。

## <a name="troubleshoot"></a>トラブルシューティング

HDInsight クラスターの作成で問題が発生した場合は、「[アクセス制御の要件](hdinsight-administer-use-portal-linux.md#create-clusters)」を参照してください。

## <a name="next-steps"></a>次のステップ
この記事では、HDInsight クラスターを作成する方法をいくつか説明しました。 詳細については、次の記事を参照してください。

* .NET クライアント ライブラリを使用したリソースのデプロイの例については、[.NET ライブラリとテンプレートを使用したリソースのデプロイ](../virtual-machines/windows/csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。
* アプリケーションのデプロイの詳細な例については、「 [Azure でマイクロサービスを予測どおりにデプロイする](../app-service/app-service-deploy-complex-application-predictably.md)」を参照してください。
* ソリューションを別の環境にデプロイする方法については、「 [Microsoft Azure の開発環境とテスト環境](../solution-dev-test-environments.md)」を参照してください。
* Azure Resource Manager のテンプレートのセクションについては、「 [Azure Resource Manager のテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)」を参照してください。
* Azure Resource Manager のテンプレートで使用できる関数の一覧については、「 [Azure Resource Manager のテンプレートの関数](../azure-resource-manager/resource-group-template-functions.md)」を参照してください。

## <a name="appendix-resource-manager-template-to-create-a-hadoop-cluster"></a>付録: Resource Manager テンプレートを使用した Hadoop クラスターの作成
次に示す Azure Resource Manager テンプレートは、Azure ストレージ アカウントに依存する Linux ベースの Hadoop クラスターを作成します。

> [!NOTE]
> このサンプルには、Hive メタストアと Oozie メタストアの構成情報が含まれています。 セクションを削除するか、テンプレートを使用する前にセクションを構成します。
>
>

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).keys[0].value]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                }
            ]
            }
        }
        }
    ],
    "outputs": {
        "cluster": {
        "type": "object",
        "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
        }
    }
    }

## <a name="appendix-resource-manager-template-to-create-a-spark-cluster"></a>付録: Resource Manager テンプレートを使用した Spark クラスターの作成

このセクションでは、Azure Resource Manager テンプレートを使用して HDInsight Spark クラスターを作成します。 このテンプレートには、`spark-defaults` と `spark-thrift-sparkconf` の構成 (Spark 1.6 クラスター用) と、`spark2-defaults` と `spark2-thrift-sparkconf` の構成 (Spark 2 クラスター用) が含まれています。 これに加え、HDInsight は、クラスターのサイズに基づいて、`spark.executor.instances`、`spark.executor.memory`、および `spark.executor.cores` などの構成を計算し、設定します。 

あるセクションで、任意の 1 つのパラメーターをテンプレート自体の一部として設定した場合、HDInsight は、同じセクションの他のパラメーターの計算および設定は行いません。 たとえば、パラメーター `spark.executor.instances` が `spark-defaults` 構成に設定されているとします。 `spark-defaults` 構成で別のパラメーター (例: `spark.yarn.exector.memoryOverhead`) を設定した場合、HDInsight は、`spark.executor.instances` パラメーターの計算と設定は行いません。

    {
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "0.9.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of the HDInsight cluster to create."
            }
        },
        "clusterLoginUserName": {
            "type": "string",
            "defaultValue": "admin",
            "metadata": {
                "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
            }
        },
        "clusterLoginPassword": {
            "type": "securestring",
            "metadata": {
                "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus",
            "metadata": {
                "description": "The location where all azure resources will be deployed."
            }
        },
        "clusterVersion": {
            "type": "string",
            "defaultValue": "3.5",
            "metadata": {
                "description": "HDInsight cluster version."
            }
        },
        "clusterWorkerNodeCount": {
            "type": "int",
            "defaultValue": 4,
            "metadata": {
                "description": "The number of nodes in the HDInsight cluster."
            }
        },
        "clusterKind": {
            "type": "string",
            "defaultValue": "SPARK",
            "metadata": {
                "description": "The type of the HDInsight cluster to create."
            }
        },
        "sshUserName": {
            "type": "string",
            "defaultValue": "sshuser",
            "metadata": {
                "description": "These credentials can be used to remotely access the cluster."
            }
        },
        "sshPassword": {
            "type": "securestring",
            "metadata": {
                "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
            }
        }
    },
    "variables": {
        "defaultApiVersion": "2017-06-01",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
    {
            "apiVersion": "2015-03-01-preview",
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "dependsOn": [],
            "properties": {
                "clusterVersion": "[parameters('clusterVersion')]",
                "osType": "Linux",
                "tier": "standard",
                "clusterDefinition": {
                    "kind": "[parameters('clusterKind')]",
                    "configurations": {
                        "gateway": {
                            "restAuthCredential.isEnabled": true,
                            "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                            "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                        },
                        "spark-defaults": {
                            "spark.executor.cores": "2"
                        },
                        "spark-thrift-sparkconf": {
                            "spark.yarn.executor.memoryOverhead": "896"
                        }
                    }
                },
                "storageProfile": {
                    "storageaccounts": [
                        {
                            "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                            "isDefault": true,
                            "container": "[parameters('clusterName')]",
                            "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).keys[0].value]"
                        }
                    ]
                },
                "computeProfile": {
                    "roles": [
                        {
                            "name": "headnode",
                            "minInstanceCount": 1,
                            "targetInstanceCount": 2,
                            "hardwareProfile": {
                                "vmSize": "Standard_D12"
                            },
                            "osProfile": {
                                "linuxOperatingSystemProfile": {
                                    "username": "[parameters('sshUserName')]",
                                    "password": "[parameters('sshPassword')]"
                                }
                            },
                            "virtualNetworkProfile": null,
                            "scriptActions": []
                        },
                        {
                            "name": "workernode",
                            "minInstanceCount": 1,
                            "targetInstanceCount": 4,
                            "hardwareProfile": {
                                "vmSize": "Standard_D4"
                            },
                            "osProfile": {
                                "linuxOperatingSystemProfile": {
                                    "username": "[parameters('sshUserName')]",
                                    "password": "[parameters('sshPassword')]"
                                    }
                                },
                                "virtualNetworkProfile": null,
                                "scriptActions": []
                            }
                        ]
                    }
                }
            }
        ]
    }
