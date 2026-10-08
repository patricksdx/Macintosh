# EFI OpenCore — Intel Core i5-12600K / ASUS ProArt B760 D4

Configuración EFI de OpenCore para un equipo con **Intel Core i5-12600K** y **ASUS ProArt B760 D4**, orientada al arranque de macOS.

## Hardware

| Componente | Modelo / estado |
| --- | --- |
| Procesador | Intel Core i5-12600K (Alder Lake) |
| Placa base | ASUS ProArt B760 D4 |
| Memoria | 32 GB DDR4 |
| Gráfica dedicada | AMD Radeon RX 6650 XT |
| Gráfica integrada | Intel UHD 770; sin aceleración gráfica compatible con macOS |
| SMBIOS configurado | `MacPro7,1` |

**Esta plataforma necesita una GPU dedicada compatible con la versión de macOS que se vaya a instalar para disponer de aceleración gráfica.** La configuración actual incluye `-wegnoigpu` para desactivar la iGPU mediante WhateverGreen.

## Estructura

```text
EFI/
├── BOOT/                 # Arranque UEFI
└── OC/
    ├── ACPI/             # Tablas ACPI
    ├── Drivers/          # Controladores UEFI
    ├── Kexts/            # Extensiones del kernel
    ├── Resources/        # Recursos de la interfaz de OpenCore
    ├── config.plist      # Configuración de OpenCore
    └── OpenCore.efi      # Cargador de arranque
```

La carpeta local `com.apple.recovery.boot/` contiene la imagen de recuperación de macOS y queda excluida de Git. Debe obtenerse por separado cuando se prepare un instalador de recuperación.

## Componentes de la configuración

Entre las extensiones referenciadas en `config.plist` se encuentran:

- **Lilu**, **VirtualSMC** y **WhateverGreen**: soporte de parches, emulación del SMC y ajustes gráficos.
- **AppleALC**: audio integrado; argumento actual `alcid=12`.
- **CpuTopologyRebuild** y **RestrictEvents**: ajustes de topología de CPU y compatibilidad.
- **SMCProcessor** y **SMCSuperIO**: sensores.
- **USBInjectAll** y **XHCI-unsupported**: soporte USB; se recomienda realizar un mapa USB específico del equipo.
- **AppleIGC**, **IntelMausi**, **AtherosE2200Ethernet**, **LucyRTL8125Ethernet** y **RealtekRTL8111**: extensiones de red incluidas en la configuración. Su presencia no implica que todos esos controladores correspondan a esta placa.

También se referencia la tabla ACPI `MaLd0n.aml` y los drivers UEFI `HfsPlus.efi`, `OpenCanopy.efi`, `OpenRuntime.efi` y `ResetNvramEntry.efi`.

### Argumentos de arranque actuales

```text
-v alcid=12 watchdog=0 agdpmod=pikera dk.e1000=0 e1000=0 npci=0x3000 -wegnoigpu
```

Estos argumentos reflejan el archivo actual y deben revisarse según la GPU, el controlador de red y el resto del hardware utilizado.

## Uso

1. Haz una copia de seguridad de tu EFI actual y conserva un USB de arranque funcional.
2. Comprueba la compatibilidad de tu GPU y del resto del hardware con la versión de macOS elegida.
3. Revisa `EFI/OC/config.plist` con [ProperTree](https://github.com/corpnewt/ProperTree), especialmente ACPI, kexts, USB y argumentos de arranque.
4. Genera una identidad SMBIOS propia para `MacPro7,1`, por ejemplo con [GenSMBIOS](https://github.com/corpnewt/GenSMBIOS), y configura de forma coherente `SystemSerialNumber`, `MLB`, `SystemUUID` y `ROM` en `PlatformInfo`. No reutilices los identificadores de otro equipo ni publiques los tuyos.
5. Valida el archivo con `ocvalidate` de la misma versión de OpenCore que los binarios de esta EFI.
6. Monta la partición EFI del USB o disco de destino y copia dentro la carpeta `EFI`, de modo que queden las rutas `EFI/BOOT` y `EFI/OC`.
7. Arranca desde la entrada UEFI correspondiente y comprueba el funcionamiento del equipo antes de usar esta EFI como arranque principal.

Para preparar el instalador y ajustar la BIOS, consulta la [guía de instalación de OpenCore de Dortania](https://dortania.github.io/OpenCore-Install-Guide/). Los ajustes deben adaptarse a esta plataforma y a los quirks de la configuración.

## Estado de compatibilidad

No se han documentado todavía la versión de OpenCore, la versión de macOS probada ni los resultados de las pruebas de audio, Ethernet, USB, suspensión y servicios de Apple. La presencia de archivos o kexts no confirma que esas funciones estén verificadas.

## Archivos excluidos de Git

El `.gitignore` excluye archivos auxiliares de macOS y Windows, imágenes de recuperación, registros de OpenCore y copias temporales del editor. Los binarios `.efi`, las tablas `.aml`, los kexts y `config.plist` se mantienen disponibles para versionar la EFI.

**Git no permite ocultar campos individuales de `config.plist` mediante `.gitignore`: revisa y elimina los identificadores personales antes de publicarlo.**

## Referencias

- [OpenCorePkg](https://github.com/acidanthera/OpenCorePkg)
- [Guía de OpenCore de Dortania](https://dortania.github.io/OpenCore-Install-Guide/)
- [Ajustes posteriores a la instalación](https://dortania.github.io/OpenCore-Post-Install/)
