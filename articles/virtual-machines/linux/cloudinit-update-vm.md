---
title: Azure에서 Linux VM에서 클라우드-init 사용
description: Azure CLI에서 cloud-init를 사용하여 생성 중인 Linux VM의 패키지를 업데이트 및 설치하는 방법
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/20/2018
ms.author: cynthn
ms.openlocfilehash: 7b7a03572a001fc6d5114635b33510f1a4b1bc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969150"
---
# <a name="use-cloud-init-to-update-and-install-packages-in-a-linux-vm-in-azure"></a>cloud-init를 사용하여 Azure의 Linux VM에서 패키지 업데이트 및 설치
이 문서에서는 Azure에서 프로비저닝 시간에 Linux 가상 컴퓨터(VM) 또는 가상 시스템 규모 집합에서 패키지를 업데이트하기 위해 [클라우드 초기화](https://cloudinit.readthedocs.io) 사용 방법을 보여 주며 있습니다. Azure에서 리소스가 프로비전되면 처음 부팅 시 이러한 cloud-init 스크립트가 실행됩니다. Azure 및 지원되는 Linux 배포판에서 클라우드 init이 기본적으로 작동하는 방식에 대한 자세한 내용은 [클라우드 init 개요를](using-cloud-init.md) 참조하세요.

## <a name="update-a-vm-with-cloud-init"></a>cloud-init를 사용하여 VM 업데이트
보안상의 이유로 최초 부팅에서 최신 업데이트를 적용하도록 VM을 구성하려 할 수 있습니다. cloud-init는 다양한 Linux 배포에서 작동하므로 패키지 관리자에 대해 `apt` 또는 `yum`을 지정할 필요가 없습니다. 그 대신 `package_upgrade`를 지정하고 cloud-init 프로세스가 사용 중인 배포에 적합한 메커니즘을 판단하게 합니다. 이 워크플로를 통해 전체 배포에서 동일한 cloud-init 스크립트를 사용할 수 있습니다.

진행 중인 업그레이드 프로세스를 확인하려면 현재 셸에 이름이 *cloud_init_upgrade.txt*인 파일을 만들고 다음 구성을 붙여넣습니다. 이 예제에서는 로컬 컴퓨터에 없는 Cloud Shell에서 파일을 만듭니다. 원하는 모든 편집기를 사용할 수 있습니다. `sensible-editor cloud_init_upgrade.txt`를 입력하여 파일을 만들고 사용할 수 있는 편집기의 목록을 봅니다. #1을 선택하여 **nano** 편집기를 사용합니다. 전체 cloud-init 파일, 특히 첫 줄이 올바르게 복사되었는지 확인합니다.  

```yaml
#cloud-config
package_upgrade: true
packages:
- httpd
```

이 이미지를 배포하기 전에 [az group create](/cli/azure/group) 명령을 사용하여 리소스 그룹을 만들어야 합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 다음 예제에서는 *동쪽* 위치에 *myResourceGroup이라는* 리소스 그룹을 만듭니다.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

이제 [az vm create](/cli/azure/vm)로 VM을 만들고 다음과 같이 `--custom-data cloud_init_upgrade.txt`로 cloud-init 파일을 지정합니다.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_upgrade.txt \
  --generate-ssh-keys 
```

이전 명령에서 출력에 표시된 VM의 공용 IP 주소에 대한 SSH. 사용자 고유의 **publicIpAddress**를 다음과 같이 입력합니다.

```bash
ssh <publicIpAddress>
```

패키지 관리 도구를 실행하여 업데이트를 확인합니다.

```bash
sudo yum update
```

cloud-init가 부팅 시 업데이트를 확인하여 설치했으므로 적용할 추가 업데이트가 없습니다.  `yum history`를 실행하여 `httpd`의 설치와 함께 업데이트 프로세스, 변경된 패키지 수를 확인하고 아래 있는 것과 유사한 출력을 검토합니다.

```bash
Loaded plugins: fastestmirror, langpacks
ID     | Command line             | Date and time    | Action(s)      | Altered
-------------------------------------------------------------------------------
     3 | -t -y install httpd      | 2018-04-20 22:42 | Install        |    5
     2 | -t -y upgrade            | 2018-04-20 22:38 | I, U           |   65
     1 |                          | 2017-12-12 20:32 | Install        |  522
```

## <a name="next-steps"></a>다음 단계
구성 변경에 대한 추가 cloud-init 예제를 보려면 다음을 참조하세요.
 
- [VM에 추가 Linux 사용자 추가](cloudinit-add-user.md)
- [패키지 관리자를 실행하여 첫 번째 부팅 시 기존 패키지 업데이트](cloudinit-update-vm.md)
- [VM 로컬 호스트 이름 변경](cloudinit-update-vm-hostname.md) 
- [애플리케이션 패키지 설치, 구성 파일 업데이트 및 키 삽입](tutorial-automate-vm-deployment.md)
