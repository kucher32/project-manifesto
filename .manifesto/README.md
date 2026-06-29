# .manifesto/ — source archive for project-manifesto framework

This folder is the source archive of the project-manifesto framework
installed in this project. It is **Category A** in the v0.7.0 three-
category artifact model: pre-built configurations for all supported
modes, plus the setup instruction.

You (the adopter) do NOT edit files inside this folder. It is replaced
wholesale when you upgrade to a new manifesto version.

## Contents

- mode-1-triad/ — pre-built files for mode-1 (Architect + Executor + Operator triad).
- mode-2-solo/ — pre-built files for mode-2 (Claude Code plays both Architect and Executor roles).

Note: setup.md lives at the repository root (next to .manifesto/), not inside this folder. Claude Code reads setup.md to install manifesto into your project.

## How to use

1) After unpacking the archive into a new or existing project directory,
   open Claude Code in that project.
2) Tell Claude Code: "read setup.md and proceed".
3) Claude Code asks which mode you want, then installs the chosen mode's
   files to the project root.

## Version

This archive was built from project-manifesto v0.7.0 (pending — currently
in development on the maintainer's working branch).

For the canonical source see github.com/kucher32/project-manifesto.
