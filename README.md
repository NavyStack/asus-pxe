# Asus PXE

Openwrt를 설치하기가 번거롭고, Merlin은 지원하지 않으며, PXE는 필요한 사용하는 상황에서,

1. 간단하게 관리자 페이지에서 SSH를 활성화하고,
2. 다음 내용을 수정하여 적절한 위치인 /jffs/configs/ 또는 /jffs/ 경로에 저장합니다.

> [!IMPORTANT]
> /jffs/ 경로에 저장한 파일은 재부팅해도 삭제되지 않지만, /etc/dnsmasq.conf 파일에 기록한 내용은 재부팅 시 삭제됩니다.

또한, iventoy 기준으로 작성되었으며 해당 문서는 [공식 사이트](https://www.iventoy.com/en/doc_ext_dhcp.html)에서 확인할 수 있습니다.

```sh
#!/bin/sh

file="/etc/dnsmasq.conf"
match_line="dhcp-match=set:UEFI,option:client-arch,7"
boot_uefi_line="dhcp-boot=tag:UEFI,iventoy_loader_16000_uefi,192.168.0.200"
boot_bios_line="dhcp-boot=tag:!UEFI,iventoy_loader_16000_bios,192.168.0.200"

if ! grep -qF "$match_line" "$file"; then
    echo "$match_line" >> "$file"
fi

if ! grep -qF "$boot_uefi_line" "$file"; then
    echo "$boot_uefi_line" >> "$file"
fi

if ! grep -qF "$boot_bios_line" "$file"; then
    echo "$boot_bios_line" >> "$file"
fi

echo "Updating PXE settings"

dnsmasq

```

Refer to [RFC4578](https://en.wikipedia.org/wiki/Preboot_Execution_Environment)
<br><br>
Dockerhub [바로가기](https://hub.docker.com/r/navystack/iventoy) / 해당 깃허브 [바로가기](https://github.com/NavyStack/iventoy-docker)
<br><br>

> [!TIP]
> 우연히 iventoy 주소가 192.168.0.200 이라면 간단히 ssh 접속후

```sh
mkdir -p /jffs/configs/ && \
curl -o /jffs/configs/pxe https://github.com/NavyStack/asus-pxe/blob/main/pxe && \
chmod +x /jffs/configs/pxe && \
sh /jffs/configs/pxe
```
