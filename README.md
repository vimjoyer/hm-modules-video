# hm-modules-video
```nix
# ./modules/myModule.nix

{ inputs, pkgs, lib, ... }:

{
  options = {
    wallpaper = lib.mkOption {
      default = ./Wallpaper1.png;
      type = lib.types.path;
      description = ''
        Path yo your wallpaper
      '';
    };
  };

  config = {
    home.file."testscript.sh".source =
      let
        script = pkgs.writeShellScriptBin "testscript.sh" ''
          ${pkgs.swww}/bin/swww img ${${edit(`./Wallpaper1.png`,`config.wallpaper`)}}
        '';
      in
      "${script}/bin/testscript.sh";
  };
}

```


```nix
# ./hyprland.nix

{ inputs, pkgs, ... }:

{

  wayland.windowManager.hyprland = {
    enable = true;
    enableNvidiaPatches = true;
    settings = {
      monitor = map
          (m:
            let
              resolution = "${toString m.width}x${toString m.height}@${toString m.refreshRate}";
              position = "${toString m.x}x${toString m.y}";
            in
            "${m.name},${if m.enabled then "${resolution},${position},1" else "disable"}"
          )
          (config.monitors);
    };
}

```

```nix
# ./modules/monitors.nix

{ lib, config, ... }:

let
  inherit (lib) mkOption types;
in
{
  options.monitors = mkOption {
    type = types.listOf (types.submodule {
      options = {
        name = mkOption {
          type = types.str;
          example = "DP-1";
        };
        width = mkOption {
          type = types.int;
          example = 1920;
        };
        height = mkOption {
          type = types.int;
          example = 1080;
        };
        refreshRate = mkOption {
          type = types.int;
          default = 60;
        };
        x = mkOption {
          type = types.int;
          default = 0;
        };
        y = mkOption {
          type = types.int;
          default = 0;
        };
        enabled = mkOption {
          type = types.bool;
          default = true;
        };
      };
    });
    default = [ ];
  };
}

```
