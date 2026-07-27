# Repository Guidelines

## Project Structure & Module Organization

The primary solution is `Source/GiaoXu - VS2015.sln`, a legacy C# WinForms application targeting .NET Framework 2.0 and x86. `Source/ChuongTrinh/` contains the main `GiaoXu` executable. Shared database and domain code lives in `Source/DBAccess/`; reusable controls and forms are in `Source/GXControl/`; reports are in `Source/ExcelReport/`. Supporting projects include `Giaoly`, `AutoUpdate`, `ConvertFont`, and `GxTranslation`. Treat `AutoCompleteTextBox/` and `TabStrip_src/` as bundled third-party/sample code unless a change specifically requires them. Runtime assets and build output are under `BIN/`; packaged installers and update archives belong in `Release/`.

## Build, Test, and Development Commands

Run commands from a Windows Developer Command Prompt with the required .NET Framework and proprietary Janus/Office dependencies available.

- `nuget restore "Source\GiaoXu - VS2015.sln"` restores packages referenced by the projects.
- `msbuild "Source\GiaoXu - VS2015.sln" /t:Build /p:Configuration=Debug /p:Platform="Any CPU"` builds the development configuration into `BIN\`.
- `msbuild "Source\GiaoXu - VS2015.sln" /t:Build /p:Configuration=Release /p:Platform="Any CPU"` creates an optimized release build.
- `BIN\GiaoXu.exe` launches the application for manual verification.

## Coding Style & Naming Conventions

Follow the existing C# style: four-space indentation, braces on new lines, `PascalCase` for types and methods, and `camelCase` for locals and parameters. Preserve established UI prefixes such as `frm` for forms and `Gx` for custom controls. Keep `.cs`, `.Designer.cs`, and `.resx` files together; use the WinForms designer for generated UI code when possible. Preserve the encoding of legacy source files and avoid unrelated formatting churn.

## Testing Guidelines

There is no automated application test suite; `TabStrip.Test` is a demonstration harness. Build the full solution, launch the application, and exercise the affected workflow with representative data. For UI changes, verify layout, validation, persistence, and error paths, and include before/after screenshots in the pull request.

## Commit & Pull Request Guidelines

History uses short descriptive subjects in English or Vietnamese, for example `Fix error when import data from MGC`. Keep each commit focused on one behavior. Pull requests should explain the change, affected projects, manual test steps, and any database or release-package impact. Link related issues when available.

## Security & Configuration

Never commit passwords, API keys, tokens, `.env` files, or parish/user databases. If `.env` usage is introduced, add it to `.gitignore` before committing. Review staged files carefully before every commit, especially changes under `BIN/` and `Release/`.
