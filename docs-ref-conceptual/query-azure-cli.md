---
title: Azure CLI를 사용한 쿼리 명령 결과
description: Azure CLI 명령의 출력에 대해 JMESPath 쿼리를 수행하는 방법을 학습합니다.
author: sptramer
ms.author: sttramer
manager: carmonm
ms.date: 11/12/2018
ms.topic: conceptual
ms.prod: azure
ms.technology: azure-cli
ms.devlang: azurecli
ms.openlocfilehash: e878c467f71423cc8c9caa1f8cfe270d0019c48b
ms.sourcegitcommit: 399f0a2997675fbb280243e4234cf63c3bbca819
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67194862"
---
# <a name="query-azure-cli-command-output"></a>Azure CLI 명령 출력 쿼리

Azure CLI는 `--query` 인수를 사용하여 명령의 결과에 대해 [JMESPath 쿼리](http://jmespath.org)를 실행합니다. JMESPath는 JSON의 쿼리 언어이며, CLI 출력의 데이터를 선택하고 수정하는 기능을 제공합니다. 다른 표시 형식 전에 이러한 쿼리를 JSON 출력에서 실행합니다.

`--query` 인수는 Azure CLI의 모든 명령에서 지원됩니다. 이 문서에서는 일련의 간단한 예제를 사용하여 JMESPath의 기능을 사용하는 방법을 설명합니다.

## <a name="dictionary-and-list-cli-results"></a>사전 및 CLI 결과 목록

JSON이 아닌 출력 형식을 사용하는 경우에도 CLI 명령 결과는 먼저 쿼리용 JSON으로 처리됩니다. CLI 결과는 JSON 배열 또는 사전입니다. 배열은 인덱싱할 수 있는 개체의 시퀀스이며 사전은 키를 사용하여 액세스되는 정렬되지 않은 개체입니다. 둘 이상의 개체를 반환할 _수 있는_ 명령은 배열을 반환하고 _항상_ 단일 개체_만_ 반환하는 명령은 사전을 반환합니다.

## <a name="get-properties-in-a-dictionary"></a>사전에 속성 가져오기

사전 결과로 작업하면 키만으로 최상위 레벨에서 속성에 액세스할 수 있습니다. `.`(__subexpression__) 문자를 사용하여 중첩된 사전의 속성을 액세스합니다. 쿼리를 도입하기 전에 `az vm show` 명령의 수정되지 않은 출력을 살펴보기:

```azurecli-interactive
az vm show -g QueryDemo -n TestVM -o json
```

명령은 사전을 출력합니다. 일부 콘텐츠는 생략 되었습니다.

```json
{
  "additionalCapabilities": null,
  "availabilitySet": null,
  "diagnosticsProfile": {
    "bootDiagnostics": {
      "enabled": true,
      "storageUri": "https://xxxxxx.blob.core.windows.net/"
    }
  },
  ...
  "osProfile": {
    "adminPassword": null,
    "adminUsername": "azureuser",
    "allowExtensionOperations": true,
    "computerName": "TestVM",
    "customData": null,
    "linuxConfiguration": {
      "disablePasswordAuthentication": true,
      "provisionVmAgent": true,
      "ssh": {
        "publicKeys": [
          {
            "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso",
            "path": "/home/azureuser/.ssh/authorized_keys"
          }
        ]
      }
    },
    "secrets": [],
    "windowsConfiguration": null
  },
  ....
}
```

다음 명령은 쿼리를 추가하여 VM에 연결할 수 있는 SSH 공개 키를 가져옵니다.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query osProfile.linuxConfiguration.ssh.publicKeys -o json
```

```json
[
  {
    "keyData": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso",
    "path": "/home/azureuser/.ssh/authorized_keys"
  }
]
```

둘 이상의 속성을 가져오려면 대괄호 `[ ]`(__다중 선택 목록__)에 쉼표로 구분된 목록으로 언어 식을 입력하십시오. VM 이름, 관리 사용자 및 SSH 키를 모두 한 번에 가져 오려면 다음 명령을 사용하십시오.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query '[name, osProfile.adminUsername, osProfile.linuxConfiguration.ssh.publicKeys[0].keyData]' -o json
```

```json
[
  "TestVM",
  "azureuser",
  "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso"
]
```

이러한 값은 쿼리에서 제공된 순서로 결과 배열에 나열됩니다. 결과가 배열이므로 결과와 연결된 키가 없습니다.

## <a name="get-a-single-value"></a>단일 값 가져오기

일반적으로 Azure 리소스 ID, 리소스 이름, 사용자 이름 또는 암호와 같은 CLI 명령에서는 _하나_ 의 값만 가져와야 합니다. 이 경우 값을 로컬 환경 변수에 저장하려는 경우도 많습니다. 단일 속성을 가져오려면 먼저 쿼리에서 하나의 속성만 가져오도록 합니다. 관리 사용자 이름만 가져오도록 마지막 예제를 다음과 같이 수정합니다.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query 'osProfile.adminUsername' -o json
```

```JSON
"azureuser"
```

여기서는 유효한 단일 값처럼 보이지만 `"` 문자가 출력의 일부로 반환됩니다. 이 문자는 개체가 JSON 문자열임을 나타냅니다. 이 값을 명령의 출력으로 환경 변수에 직접 할당하면 셸에서 따옴표가 해석되지 __않을__ 수도 있습니다.

```bash
USER=$(az vm show -g QueryDemo -n TestVM --query 'osProfile.adminUsername' -o json)
echo $USER
```

```output
"azureuser"
```

이는 거의 확실히 원하는 작업이 아닙니다. 이 경우 반환된 값을 형식 정보와 함께 포함하지 않는 출력 형식을 사용할 수 있습니다. 이 용도를 위해 CLI에서 제공하는 최상의 출력 옵션은 `tsv`(탭으로 구분된 값)입니다. 특히 단일 값(사전이나 목록이 아닌)인 값만 검색할 때 `tsv` 출력이 인용되지 않도록 보장됩니다.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query 'osProfile.adminUsername' -o tsv
```

```output
azureuser
```

`tsv` 출력 형식에 대한 자세한 내용은 [출력 형식 - TSV 출력 형식](format-output-azure-cli.md#tsv-output-format)을 참조하세요.

## <a name="rename-properties-in-a-query"></a>쿼리에서 속성 이름 바꾸기

여러 값을 쿼리할 때 배열 대신 사전을 가져오려면 `{ }`(__다중 선택 해시__) 연산자를 사용하십시오.
다중 선택 해시 양식은 `{displayName:JMESPathExpression, ...}`입니다.
`displayName`은 출력에 표시되는 문자열이며 `JMESPathExpression`은 평가할 JMESPath 식입니다. 다중 선택 목록을 해시로 변경하여 마지막 섹션에서 예제를 수정하는 방법은 다음과 같습니다.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query '{VMName:name, admin:osProfile.adminUsername, sshKey:osProfile.linuxConfiguration.ssh.publicKeys[0].keyData }' -o json
```

```json
{
  "VMName": "TestVM",
  "admin": "azureuser",
  "ssh-key": "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso"
}
```

## <a name="get-properties-in-an-array"></a>배열에 속성 가져오기

배열 자체에 대한 속성은 없지만 인덱싱할 수 있습니다. 이 기능은 마지막 예제에서 `publicKeys[0]` 표현식과 함께 표시되며 `publicKeys` 배열의 첫 번째 요소를 가져옵니다. CLI 출력이 명령되었는지 보장할 수 없으므로 명령에 대해 확신하거나 어떤 요소를 얻을지 신경 쓰지 않는 한 색인을 사용하지 마십시오. 배열의 요소 속성에 액세스하려면 _평면화_ 및 _필터링_ 중 하나를 수행합니다. 이 섹션에서는 배열을 평면화하는 방법을 설명합니다.

배열을 평면화는 `[]` JMESPath 연산자를 사용 하여 이루어집니다. `[]` 연산자 다음의 모든 표현식은 현재 배열의 각 요소에 적용됩니다.
쿼리 시작 부분에 `[]`이 나타나면 CLI 명령 결과가 평면화됩니다. `az vm list`의 결과는 이 기능을 사용하여 검사할 수 있습니다.
리소스 그룹의 각 VM에 대한 이름, OS 및 관리자 이름을 가져오려면:

```azurecli-interactive
az vm list -g QueryDemo --query '[].{Name:name, OS:storageProfile.osDisk.osType, admin:osProfile.adminUsername}' -o json
```

```json
[
  {
    "Name": "Test-2",
    "OS": "Linux",
    "admin": "sttramer"
  },
  {
    "Name": "TestVM",
    "OS": "Linux",
    "admin": "azureuser"
  },
  {
    "Name": "WinTest",
    "OS": "Windows",
    "admin": "winadmin"
  }
]
```

`--output table` 출력 양식과 결합하면 열 이름이 다중 선택 해시의 `displayKey` 값과 일치합니다.

```azurecli-interactive
az vm list -g QueryDemo --query '[].{Name:name, OS:storageProfile.osDisk.osType, Admin:osProfile.adminUsername}' --output table
```

```output
Name     OS       Admin
-------  -------  ---------
Test-2   Linux    sttramer
TestVM   Linux    azureuser
WinTest  Windows  winadmin
```

> [!NOTE]
>
> 특정 키는 필터링되고 테이블 보기에 인쇄되지 않습니다. 이러한 키는 `id`, `type` 및 `etag`입니다. 이러한 값을 보려면 multiselect 해시 키 이름을 변경할 수 있습니다.
>
> ```azurecli-interactive
> az vm show -g QueryDemo -n TestVM --query "{objectID:id}" -o table
> ```

명령으로 반환된 최상위 결과뿐만 아니라 모든 배열을 평면화할 수 있습니다. 마지막 섹션에서는 `osProfile.linuxConfiguration.ssh.publicKeys[0].keyData` 식이 로그인에 대한 SSH 공개 키를 가져오기 위해 사용되었습니다. _모든_ SSH 공개 키를 얻으려면 식을 대신 `osProfile.linuxConfiguration.ssh.publicKeys[].keyData`로 작성하십시오.
이 쿼리 식은 `osProfile.linuxConfiguration.ssh.publicKeys` 배열을 평면화한 다음 각 요소에서 `keyData` 식을 실행합니다.

```azurecli-interactive
az vm show -g QueryDemo -n TestVM --query '{VMName:name, admin:osProfile.adminUsername, sshKeys:osProfile.linuxConfiguration.ssh.publicKeys[].keyData }' -o json
```

```json
{
  "VMName": "TestVM",
  "admin": "azureuser",
  "sshKeys": [
    "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDMobZNJTqgjWn/IB5xlilvE4Y+BMYpqkDnGRUcA0g9BYPgrGSQquCES37v2e3JmpfDPHFsaR+CPKlVr2GoVJMMHeRcMJhj50ZWq0hAnkJBhlZVWy8S7dwdGAqPyPmWM2iJDCVMVrLITAJCno47O4Ees7RCH6ku7kU86b1NOanvrNwqTHr14wtnLhgZ0gQ5GV1oLWvMEVg1YFMIgPRkTsSQKWCG5lLqQ45aU/4NMJoUxGyJTL9i8YxMavaB1Z2npfTQDQo9+womZ7SXzHaIWC858gWNl9e5UFyHDnTEDc14hKkf1CqnGJVcCJkmSfmrrHk/CkmF0ZT3whTHO1DhJTtV stramer@contoso\n"
  ]
}
```

## <a name="filter-arrays"></a>배열 필터링

배열에서 데이터를 가져오는 데 사용되는 다른 작업은 _필터링_ 입니다. 필터링은 `[?...]` JMESPath 연산자를 사용하여 이루어집니다.
이 연산자는 내용에 맞게 조건자를 사용합니다. 조건자는 `true` 또는 `false`로 평가할 수 있는 문입니다. 조건자가 `true`로 평가되는 표현식은 출력에 포함됩니다.

JMESPath는 표준 비교 및 논리 연산자를 제공합니다. `<`, `<=`, `>`, `>=`, `==`, `!=`을 포함합니다.
JMESPath은 또한 논리 and(`&&`), or(`||`), not(`!`)을 지원합니다. 괄호 안에 식은 그룹화하여 더 복잡한 조건자 식이 가능합니다. 조건자 및 논리 조작에 대한 전체 세부 정보는 [JMESPath 사양](http://jmespath.org/specification.html)을 참조하세요.

마지막 섹션에서는 리소스 그룹의 모든 VM의 전체 목록을 가져오기 위해 배열을 평면화했습니다. 필터를 사용하여, 이 출력은 Linux VM으로만 제한할 수 있습니다.

```azurecli-interactive
az vm list -g QueryDemo --query "[?storageProfile.osDisk.osType=='Linux'].{Name:name,  admin:osProfile.adminUsername}" --output table
```

```output
Name    Admin
------  ---------
Test-2  sttramer
TestVM  azureuser
```

> [!IMPORTANT]
>
> JMESPath에서 문자열은 항상 작은 따옴표(`'`)로 둘러싸입니다. 필터 조건자에 있는 문자열의 일부로 큰 따옴표를 사용하는 경우 빈 출력을 얻게 됩니다.

JMESPath에는 또한 필터링에 도움이 될 수 있는 기본 제공 함수도 있습니다. 이러한 함수 중 하나는 `contains(string, substring)`로서, 문자열에 부분 문자열에 포함되어 있는지를 확인 합니다. 표현식은 함수를 호출하기 전에 평가되므로 첫 번째 인수가 전체 JMESPath 표현식이 될 수 있습니다. 다음 예제에서는 SSD 스토리지를 사용하여 해당 OS 디스크에 대한 모든 VM을 찾습니다.

```azurecli-interactive
az vm list -g QueryDemo --query "[?contains(storageProfile.osDisk.managedDisk.storageAccountType,'SSD')].{Name:name, Storage:storageProfile.osDisk.managedDisk.storageAccountType}" -o json
```

```json
[
  {
    "Name": "TestVM",
    "Storage": "StandardSSD_LRS"
  },
  {
    "Name": "WinTest",
    "Storage": "StandardSSD_LRS"
  }
]
```

이 쿼리는 약간 깁니다. `storageProfile.osDisk.managedDisk.storageAccountType` 키는 두 번 언급되며 출력에서 키가 다시 입력됩니다. 이를 줄이는 한 가지 방법은 데이터를 평면화 하고 선택한 후 필터링을 적용하는 것입니다.

```azurecli-interactive
az vm list -g QueryDemo --query "[].{Name:name, Storage:storageProfile.osDisk.managedDisk.storageAccountType}[?contains(Storage,'SSD')]" -o json
```

```json
[
  {
    "Name": "TestVM",
    "Storage": "StandardSSD_LRS"
  },
  {
    "Name": "WinTest",
    "Storage": "StandardSSD_LRS"
  }
]
```

대형 배열의 경우, 데이터를 선택하기 전에 필터를 적용하는 것이 더 빠를 수 있습니다.

전체 함수 목록은 [JMESPath 스펙 - 내장 함수](http://jmespath.org/specification.html#built-in-functions)를 참조하십시오.

## <a name="change-output"></a>출력 변경

JMESPath 함수는 쿼리 결과 대해 작동하는, 다른 목적을 가질 수도 있습니다. 부울이 아닌 값을 반환하는 모든 함수는 식의 결과를 변경합니다.
예를 들어, `sort_by(array, &sort_expression)`를 사용하여 속성 값으로 데이터를 정렬할 수 있습니다. JMESPath는 특수 연산자(`&`)를 사용하여, 함수의 일부로 나중 평가되어야 하는 식에 사용됩니다. 다음 예제에서는 OS 디스크 크기에 따라 VM 목록을 정렬하는 방법을 보여 줍니다.

```azurecli-interactive
az vm list -g QueryDemo --query "sort_by([].{Name:name, Size:storageProfile.osDisk.diskSizeGb}, &Size)" --output table
```

```output
Name     Size
-------  ------
TestVM   30
Test-2   32
WinTest  127
```

전체 함수 목록은 [JMESPath 스펙 - 내장 함수](http://jmespath.org/specification.html#built-in-functions)를 참조하십시오.

## <a name="experiment-with-queries-interactively"></a>대화형으로 쿼리를 사용하여 실험

JMESPath 실험을 시작하기 위해 [JMESPath-터미널](https://github.com/jmespath/jmespath.terminal) Python 패키지는 쿼리 작업을 할 수 있는 대화형 환경을 제공합니다. 데이터는 입력으로 파이프되고 쿼리는 편집기에서 작성 및 실행됩니다.

```bash
pip install jmespath-terminal
az vm list --output json | jpterm
```
