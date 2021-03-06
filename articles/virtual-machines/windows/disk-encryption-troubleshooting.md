---
title: Azure Disk Encryption 문제 해결 가이드
description: 이 문서에서는 Windows VM용 Microsoft Azure 디스크 암호화에 대한 문제 해결 팁을 제공합니다.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: fafe4a9ef78a92595eaae942fa5d7cbeb2c07189
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458218"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Azure Disk Encryption 문제 해결 가이드

이 가이드는 조직에서 Azure Disk Encryption을 사용하는 IT 전문가, 정보 보안 분석가 및 클라우드 관리자를 위한 것입니다. 이 문서는 디스크 암호화 관련 문제를 해결하는 데 도움을 드리기 위해 작성되었습니다.

아래 단계를 수행하기 전에 먼저 암호화하려는 VM이 [지원되는 VM 크기 및 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)에 속해 있는지, 그리고 모든 필수 구성 조건을 충족되었는지 확인합니다.

- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [그룹 정책 요구 사항](disk-encryption-overview.md#group-policy-requirements)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Disk Encryption 문제 해결

방화벽, 프록시 요구 사항 또는 NSG(네트워크 보안 그룹) 설정으로 연결이 제한되면 필요한 작업을 수행할 수 있는 확장의 기능이 중단될 수 있습니다. 이로 인해 "VM에서 사용할 수 없는 확장 상태"와 같은 상태 메시지가 표시됩니다. 예상된 시나리오에서 암호화가 완료되지 못할 수 있습니다. 다음 섹션에서는 조사할 수 있는 몇 가지 일반적인 방화벽 문제가 나와 있습니다.

### <a name="network-security-groups"></a>네트워크 보안 그룹
적용되는 모든 네트워크 보안 그룹 설정은 엔드포인트에서도 디스크 암호화에 대해 문서화된 네트워크 구성 [필수 조건](disk-encryption-overview.md#networking-requirements)을 충족해야 합니다.

### <a name="azure-key-vault-behind-a-firewall"></a>방화벽 뒤에 있는 Azure Key Vault

[Azure AD 자격 증명](disk-encryption-windows-aad.md#)에 암호화가 사용하도록 설정된 경우 대상 VM은 Azure Active Directory 엔드포인트 및 Key Vault 엔드포인트 모두에 대한 연결을 허용해야 합니다. 현재 Azure Active Directory 인증 엔드포인트는 [Office 365 URL 및 IP 주소 범위](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) 설명서의 56 및 59 섹션에서 유지 관리됩니다. Key Vault 지침은 [방화벽 뒤에 있는 Azure Key Vault에 액세스](../../key-vault/general/access-behind-firewall.md)하는 방법에 관한 설명서에서 제공됩니다.

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service 
VM은 VM 내에서만 액세스할 수 있는 잘 알려진 라우팅이 불가능한 IP 주소(`169.254.169.254`)를 사용하는 [Azure Instance Metadata 서비스](../windows/instance-metadata-service.md) 엔드포인트에 액세스할 수 있어야 합니다.  이 주소로 로컬 HTTP 트래픽을 변경하는 프록시 구성(예: X-Forwarded-For 헤더 추가)은 지원되지 않습니다.

## <a name="troubleshooting-windows-server-2016-server-core"></a>Windows Server 2016 Server Core 문제 해결

Windows Server 2016 Server Core에서 bdehdcfg 구성 요소는 기본적으로 사용할 수 없습니다. 이 구성 요소는 Azure Disk Encryption에 필요합니다. VM의 수명 동안 한 번만 수행되는 OS 볼륨에서 시스템 볼륨을 분할하는 데 사용됩니다. 이러한 이진 파일은 이후 암호화 작업 중 필요하지 않습니다.

이 문제를 해결하려면 Windows Server 2016 Data Center VM의 다음 4개 파일을 Server Core의 동일한 위치에 복사합니다.

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. 다음 명령을 입력합니다.

   ```
   bdehdcfg.exe -target default
   ```

1. 이 명령은 550MB 시스템 파티션을 만듭니다. 시스템 다시 부팅합니다.

1. DiskPart를 사용하여 볼륨을 확인한 다음 계속합니다.  

다음은 그 예입니다.

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>암호화 상태 문제 해결 

포털은 VM 내에서 암호화되지 않은 후에도 디스크를 암호화된 것으로 표시할 수 있습니다.  이 문제는 상위 수준 Azure Disk 암호화 관리 명령을 사용하는 대신 VM 내에서 디스크의 암호를 직접 해독하는 데 하위 수준 명령을 사용하는 경우에 발생할 수 있습니다.  상위 수준 명령은 VM 내에서 디스크의 암호를 해독할 뿐만 아니라 VM 외부에서도 VM과 관련된 중요한 플랫폼 수준 암호화 설정 및 확장 설정을 업데이트합니다.  이러한 설정이 정렬되지 않으면 플랫폼에서 암호화 상태를 보고하거나 VM을 올바르게 프로비전할 수 없습니다.   

PowerShell을 사용하여 Azure 디스크 암호화를 사용하지 않으려면 [사용 안 함-AzVMDisk암호화](/powershell/module/az.compute/disable-azvmdiskencryption) 를 사용하고 [제거-AzVMDisk암호화 확장.](/powershell/module/az.compute/remove-azvmdiskencryptionextension) 암호화를 사용하지 않도록 설정하기 전에 제거-AzVMDisk암호화확장 실행은 실패합니다.

CLI를 사용하여 Azure 디스크 암호화를 사용하지 않으려면 [az vm 암호화를 사용하지 않도록 설정합니다.](/cli/azure/vm/encryption) 

## <a name="next-steps"></a>다음 단계

이 문서에서는 일반적인 Azure Disk Encryption 문제와 해당 문제 해결 방법에 대해 자세히 알아보았습니다. 이 서비스 및 기능에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure Security Center에서 디스크 암호화 적용](../../security-center/security-center-apply-disk-encryption.md)
- [휴지 상태의 Azure 데이터 암호화](../../security/fundamentals/encryption-atrest.md)
