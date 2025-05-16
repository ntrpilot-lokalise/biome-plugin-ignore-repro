# Biome Ignore Bug Reproduction

This repository contains a minimal reproduction of a bug in Biome where CSS linting ignores are not respected in CLI commands, despite working correctly in the IDE.

## Project Structure

This is a simple node project with the following key parts:

- `src/style.css` - Contains CSS with both regular linting errors and ignored errors
- `biome.json` - Local configuration that extends a global organization-wide config
- `base-config/` - Contains the organization's base Biome configuration
- `base-config/plugins/` - Custom Biome plugins used across projects

### Configuration Structure

Our Biome configuration is designed to work across multiple projects in the organization:

1. The local `biome.json` extends a base configuration:
   ```json
   {
     "$schema": "./node_modules/@biomejs/biome/configuration_schema.json",
     "extends": ["./base-config/biome.json"]
   }
   ```

2. The `base-config/biome.json` contains organization-wide rules and settings

3. Custom plugins (like the `noManualZIndex` rule) are defined in the `base-config/plugins/` directory

## Bug Description

When using the `biome-ignore` comment directive in a CSS file, the directive works correctly in JetBrains IDE, but the CLI commands ignore the directive and still report the error.

Related GitHub links:
- Original Discussion: [https://github.com/biomejs/biome/discussions/5854](https://github.com/biomejs/biome/discussions/5854)
- Bug Report: [https://github.com/biomejs/biome/issues/6031](https://github.com/biomejs/biome/issues/6031)

### Steps to Reproduce

1. Clone this repository
2. Install dependencies with `npm install`
3. Examine the `src/style.css` file - note there are two CSS rules:
   - One without an ignore directive (should be reported)
   - One with an ignore directive (should be ignored)
4. Run the linting command to see the incorrect behavior:
   ```
   npm run lint
   ```
   Note that both errors are reported, even the one with the ignore directive

5. Similarly, you can run the format command:
   ```
   npm run format
   ```
   And observe that the ignore directive is not respected

6. OPTIONAL - Open the project in a JetBrains IDE with the biome plugin installed and configured to see the correct behaviour - only the non-ignored error is highlighted

### Expected vs. Actual Behavior

- **Expected**: Both the IDE and CLI should respect the `biome-ignore` directive and not report the ignored error
- **Actual**: The JetBrains IDE correctly respects the ignore directive, but the CLI commands (`npm run lint` and `npm run format`) report errors that should be ignored

## Environment Information

- Biome version: 2.0.0-beta.4
- IDE: JetBrains IDE
- Operating System: MacOS