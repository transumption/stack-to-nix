stack-to-nix
===========

This is a handy nix function for building stack projects with nix, fully inside the nix sandbox.
It parses the stack.yaml file and translates the dependencies into a series of fetchUrl, fetchGit calls.

Example
=======

```nix
{ pkgs }:

let stackToNix = pkgs.callPackage (fetchTarball https://github.com/serokell/stack-to-nix/master.tar.gz) { };
in
stackToNix {
  # root: the path with stack.yaml. you may want to filter this. for example using nix-gitignore.
  root = ./.;
  # shell: get the .env instead of the nix-build derivation. recommended that you do this with shell.nix/default.nix.
  # see https://github.com/DisciplinaOU/disciplina/blob/master/shell.nix
  shell = pkgs.lib.inNixShell;
  # you shouldn't need overrides, but you can ;)
  overrides = final: previous: with pkgs.haskell.lib; {
    qtah = overrideCabal previous.qtah (super: {
      libraryToolDepends = with pkgs.qt5; [ qtbase qttools ];
    });
  };
}
```
