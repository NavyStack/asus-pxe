# Asus PXE

Openwrt 올리기는 부담스럽고, Merlin은 지원하지 않고 PXE는 필요할 때,
간단히 관리자 페이지에서 SSH 활성화 후에
아래를 적절하게 수정 하여 /jffs/configs/ 등 /jffs/경로에 저장

iventoy 기준으로 작성함 [링크](https://www.iventoy.com/en/doc_ext_dhcp.html)

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

dockerhub [바로가기](https://hub.docker.com/r/navystack/iventoy) / 해당 깃허브 [바로가기](https://github.com/NavyStack/iventoy-docker)

우연히 iventoy 주소가 192.168.0.200 이라면 간단히 ssh 접속후

mkdir -p /jffs/configs/ && \
curl -o /jffs/configs/pxe https://github.com/NavyStack/asus-pxe/blob/main/pxe && \
chmod +x /jffs/configs/pxe && \
sh /jffs/configs/pxe
