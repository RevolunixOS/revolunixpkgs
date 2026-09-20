# RevolunixOS packages

Combined Nix package set for the small desktop utilities maintained in the
RevolunixOS organization. Packages are discovered recursively from `pkgs/` and
injected into a configured Nixpkgs instance returned directly by the flake.

## Included packages

The collection currently includes Rofi front ends for Bluetooth, Wi-Fi, audio,
power, screenshots, music, and virtual machines, plus the `ide`, `backup-cli`,
`font-fixer`, `global-fullscreen`, Citra, and related helper packages.

## Use as a flake input

```nix
inputs.revolunixpkgs.url = "github:RevolunixOS/revolunixpkgs";
```

The current flake does not expose a conventional `overlays.default` output.
Instead, the input itself is the configured package set:

```nix
outputs = { self, revolunixpkgs, ... }: {
  nixosConfigurations.my-host = revolunixpkgs.purepkgs.lib.nixosSystem {
    system = "x86_64-linux";
    specialArgs.pkgs = revolunixpkgs;
    modules = [
      ({ ... }: {
        environment.systemPackages = [
          revolunixpkgs.rofi-bluetooth
          revolunixpkgs.rofi-hyprshot
          revolunixpkgs.rofi-power
        ];
      })
    ];
  };
};
```

Individual top-level package outputs can also be built directly, subject to the
current flake layout:

```bash
nix build github:RevolunixOS/revolunixpkgs#rofi-power
```

To inspect the exact attributes exported by a revision:

```bash
nix flake show github:RevolunixOS/revolunixpkgs
```

Because this output shape is non-standard, consumers may prefer to refactor the
repository to expose conventional `packages`, `legacyPackages`, and
`overlays.default` outputs.

## Development

Each package lives in its own directory:

```text
pkgs/<name>/package.nix
```

Run formatting and build the target output before opening a pull request:

```bash
nix fmt
nix build .#<package-name>
```

## Known limitations

- Inputs currently reference the legacy `RevoluNix` namespace for the module
  repositories.
- The internal overlay selects packages through a fixed `x86_64-linux` system
  variable, even though a package set is calculated for several systems.
- Most utilities were designed for one Hyprland workstation and are not yet
  portable without review.
- The primary Nixpkgs input is pinned to NixOS 24.05.

## License

See [`LICENSE`](LICENSE). Individual packaged projects may use their own
licenses; consult their source and package metadata as well.
