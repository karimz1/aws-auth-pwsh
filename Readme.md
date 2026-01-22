# AWS Authentication PowerShell Utilities

[![PowerShell](https://img.shields.io/badge/PowerShell-7.x+-blue.svg?logo=powershell&logoColor=white)](https://github.com/PowerShell/PowerShell)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Sponsors](https://img.shields.io/badge/Sponsor-❤-ff69b4?logo=github-sponsors)](https://github.com/sponsors/karimz1)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)

This repository contains a collection of minimalist PowerShell scripts designed to streamline authentication for AWS CodeArtifact (NuGet) and Amazon ECR (Docker).

These utilities were originally developed as personal presets to eliminate the friction of manually executing repetitive AWS CLI commands. While the logic is straightforward, these scripts provide a reliable, cross-platform way to manage local NuGet authentication for private .NET registries and Docker registry access for container workflows.

The scripts function as transparent wrappers for the official **AWS CLI v2**. By utilizing PowerShell Core (**pwsh**), they maintain consistent behavior across Windows, macOS, and Linux.

------

## Key Features

- **Automated Token Management**: Simplifies the process of refreshing NuGet and Docker credentials.
- **Cross-Platform**: Built with `pwsh` to ensure compatibility across all major operating systems.
- **Minimalist Design**: The code is concise and transparent, making it easy to audit or integrate into automated pipelines.
- **SSO Ready**: Native support for AWS Single Sign-On (SSO) workflows.

------

## Prerequisites

To use these scripts, you must have the official **AWS CLI v2** installed and configured on your system.

------

## Quick Start

How to use it.



For local development using AWS SSO, include the `-SSO` flag to trigger the browser-based login. If you use static IAM credentials (Access Key and Secret Key) already configured in your AWS profile, you may omit this flag.

``` bash
git clone https://github.com/karimz1/aws-auth-pwsh.git    
pwsh ./aws-auth-pwsh/refreshNugetToken.ps1 -SSO # auth nuget using code artifact
pwsh ./aws-auth-pwsh/refreshEcrDockerToken.ps1 -SSO # auth docker using ecr
```

------

## GitHub Actions Integration

These scripts are highly effective in CI/CD environments. Below is an example of how they can be used within a GitHub Action to authenticate a private repository.

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    defaults:
      run:
        shell: pwsh
    steps:
      - uses: actions/checkout@v4
      
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '8.0.x'
          
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: arn:aws:iam::MyAwsAccount:role/Github_Actions_Workflow_Role
          aws-region: us-east-1
          
      - name: Refresh NuGet tokens from CodeArtifact
        run: |
          git clone https://github.com/karimz1/aws-auth-pwsh.git
          ./AWS-Authentication-Scripts/refreshNugetToken.ps1
```

I regularly update the scripts if something breaks, so you can just git clone the repo. **The script filenames will never change for consistency.** I use these scripts in my personal projects as well, so this repo is the single source of truth.

## Usage Details

### AWS CodeArtifact (NuGet)

The `refreshNugetToken.ps1` script authenticates your local environment to interact with private NuGet registries hosted on AWS.

- **Standard:** `./refreshNugetToken.ps1`
- **With SSO Login:** `./refreshNugetToken.ps1 -SSO`
- **Debug Mode:** `./refreshNugetToken.ps1 -DEBUG $true`

### 2. AWS ECR (Docker)

The `refreshEcrDockerToken.ps1` script automates the `docker login` process for your Amazon ECR repositories.

- **Standard:** `./refreshEcrDockerToken.ps1`
- **With SSO Login:** `./refreshEcrDockerToken.ps1 -SSO`

------

## Security Note

For local development, I recommend utilizing the `-SSO` flag. Authenticating via SSO is a security best practice, as it avoids the use of long-term, hard-coded credentials on your local machine.

## License

This project is released under the MIT License.
