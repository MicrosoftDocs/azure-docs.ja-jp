---
title: Ansible を使用して Azure の仮想マシン スケール セットを自動的にスケーリングする
description: Ansible を使用して、Azure 内の自動スケールで仮想マシン スケール セットを拡大縮小する方法について説明します
ms.service: azure
keywords: ansible, azure, devops, bash, プレイブック, スケール, 自動スケール, 仮想マシン, 仮想マシン スケール セット, vmss
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/10/2018
ms.openlocfilehash: 578ad3207f62e74805be056ca11d3bd9b46513da
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792431"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-azure-using-ansible"></a>Ansible を使用して Azure の仮想マシン スケール セットを自動的にスケーリングする
Ansible を使用すると、環境でのリソースの展開と構成を自動化することができます。 Ansible を使用すると、他の Azure リソースを管理するのと同様に、Azure 内の仮想マシン スケール セット (VMSS) を管理できます。 

スケール セットを作成するときに、実行する VM インスタンスの数を定義します。 アプリケーションの需要の変化に応じて、VM インスタンスの数を自動的に増減することができます。 自動スケールにより、顧客のニーズに対応したり、アプリのライフサイクル全体でアプリケーション パフォーマンスの変化に対応したりできます。 この記事では、自動スケール設定を作成して既存の仮想マシン スケール セットに関連付けます。 自動スケール設定では、必要に応じてスケール アウトまたはスケールインするルールを構成できます。

## <a name="prerequisites"></a>前提条件
- **Azure サブスクリプション** - Azure サブスクリプションをお持ちでない場合は、開始する前に[無料のアカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)を作成してください。
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]
- 既存の Azure 仮想マシン スケール セット。 - 存在しない場合は、[Ansible を使用して Azure の仮想マシン スケール セットを作成します](https://docs.microsoft.com/azure/ansible/ansible-create-configure-vmss)。

> [!Note]
> このチュートリアルでは、次のサンプルのプレイブックを実行するのに Ansible 2.7 が必要です。 

## <a name="auto-scale-based-on-a-schedule"></a>スケジュールに基づく自動スケール   
スケール セットで自動スケールを有効にするには、最初に自動スケール プロファイルを定義します。 このプロファイルでは、スケール セット容量の既定値、最小値、および最大値が定義されます。 これらの制限により、VM インスタンスが継続的に作成されないようにしてコストを制御し、許容されるパフォーマンスと、スケールイン イベントに残るインスタンスの最小数のバランスをとることができます。 

Virtual Machine Scale Sets では、定期的なスケジュール、または特定の日付によってスケールインおよびスケールアウトを行うことができます。 このセクションでは、太平洋標準時で毎週月曜日の 10 時 00 分にスケール セット内の VM インスタンスの数を 3 に増やす自動スケール設定を作成する Ansible プレイブックのサンプルを使用します。 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks: 
    - name: Create auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         target:
           namespace: "Microsoft.Compute"
           types: "virtualMachineScaleSets"
           name: "{{ vmss_name }}"
         enabled: true
         profiles:
         - count: '3'
           min_count: '3'
           max_count: '3'
           name: Auto created scale condition
           recurrence_timezone: Pacific Standard Time
           recurrence_frequency: Week
           recurrence_days:
              - Monday
           recurrence_mins:
              - '0'
           recurrence_hours:
              - '10'
```

このプレイブックを *vmss-auto-scale.yml* として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook vmss-auto-scale.yml
```

## <a name="auto-scale-based-on-performance-data"></a>パフォーマンス データに基づく自動スケール
アプリケーションの需要が増加すると、スケール セット内の VM インスタンスに対する負荷が増加します。 この増加した負荷が短期的な需要ではなく持続したものである場合は、スケール セット内の VM インスタンスの数を増やす自動スケール ルールを構成できます。 これらの VM インスタンスが作成され、アプリケーションがデプロイされると、スケール セットはロード バランサーを通じてそれらへのトラフィックの分散を開始します。 監視するメトリック (CPU やディスクなど)、指定されたしきい値をアプリケーションの負荷が満たす必要がある期間、およびスケール セットに追加する VM インスタンスの数を制御します。

Virtual Machine Scale Sets では、パフォーマンス メトリックのしきい値、定期的なスケジュール、または特定の日付に基づいてスケールインおよびスケールアウトを行うことができます。 このセクションでは、太平洋標準時で毎週月曜日の 18 時 00 分に過去 10 分間のワークロードを検査し、CPU 使用率のメトリックに応じてスケール セットの VM インスタンスを 4 にスケールアウトするか、1 にスケールインする Ansible プレイブックのサンプルを使用します。 

```yml
---
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    vmss_name: myVMSS
    name: autoscalesetting
  tasks:
  - name: Get facts of the resource group
    azure_rm_resourcegroup_facts:
      name: "{{ resource_group }}"
    register: rg

  - name: Get VMSS resource uri
    set_fact:
      vmss_id: "{{ rg.ansible_facts.azure_resourcegroups[0].id }}/providers/Microsoft.Compute/virtualMachineScaleSets/{{ vmss_name }}"
    
  - name: Create auto scaling
    azure_rm_autoscale:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      target: "{{ vmss_id }}"
      enabled: true
      profiles:
      - count: '1'
        max_count: '1'
        min_count: '1'
        name: 'This scale condition is executed when none of the other scale condition(s) match'
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
      - count: '1'
        min_count: '1'
        max_count: '4'
        name: Auto created scale condition
        recurrence_days:
          - Monday
        recurrence_frequency: Week
        recurrence_hours:
          - 18
        recurrence_mins:
          - 0
        recurrence_timezone: Pacific Standard Time
        rules:
          - cooldown: 5
            direction: Increase
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: GreaterThan
            statistic: Average
            threshold: 70
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
          - cooldown: 5
            direction: Decrease
            metric_name: Percentage CPU
            metric_resource_uri: "{{ vmss_id }}"
            operator: LessThan
            statistic: Average
            threshold: 30
            time_aggregation: Average
            time_grain: 1
            time_window: 10
            type: ChangeCount
            value: '1'
```

このプレイブックを *vmss-auto-scale-metrics.yml* として保存します。 Ansible プレイブックを実行するには、次のような **ansible-playbook** コマンドを使用します。

```bash
ansible-playbook vmss-auto-scale-metrics.yml
```

## <a name="get-information-for-existing-autoscale-settings"></a>既存の自動スケール設定の情報を取得する
次のようにプレイブックを使用して、*azure_rm_autoscale_facts* モジュール経由で自動スケール設定の詳細を取得することができます。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Retrieve auto scale settings information
      azure_rm_autoscale_facts:
        resource_group: "{{ resource_group }}"
        name: "{{ name }}"
      register: autoscale_query
    
    - debug:
        var: autoscale_query.autoscales[0]
```

## <a name="disable-the-autoscale-settings"></a>自動スケール設定を無効にする
`enabled: true` を `enabled: false` に変更するか、次のようにプレイブックを使用して自動スケール設定を削除することによって、自動スケール設定を無効にできます。

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
    name: autoscalesetting
  tasks: 
    - name: Delete auto scaling
      azure_rm_autoscale:
         resource_group: "{{ resource_group }}"
         name: "{{ name }}"
         state: absent
```

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"] 
> [仮想マシン スケール セットのための Ansible サンプル プレイブック](https://github.com/Azure-Samples/ansible-playbooks/tree/master/vmss)