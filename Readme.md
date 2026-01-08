# AWS Authentication PWSH 🚀

[![PowerShell](https://img.shields.io/badge/PowerShell-7.x+-blue.svg?logo=powershell&logoColor=white)](https://github.com/PowerShell/PowerShell)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Sponsors](https://img.shields.io/badge/Sponsor-❤-ff69b4?logo=github-sponsors)](https://github.com/sponsors/karimz1)
[![Contributions Welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)

> **Streamline AWS authentication** for CodeArtifact (NuGet) and Amazon ECR (Docker) with powerful, cross-platform PowerShell automation scripts featuring integrated AWS SSO support.

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Prerequisites](#-prerequisites)
- [Installation](#-installation)
- [Quick Start](#-quick-start)
- [Usage](#-usage)
  - [Refresh NuGet Tokens](#1-refresh-nuget-tokens-for-aws-codeartifact)
  - [Authenticate Docker with ECR](#2-authenticate-docker-with-aws-ecr)
- [Automation](#-automate-session-refresh)
- [IAM Permissions](#-iam-permissions)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 Overview

This repository provides enterprise-ready PowerShell scripts designed to **automate and simplify AWS authentication** for critical development workflows. Whether you're working with AWS CodeArtifact for NuGet package management or Amazon ECR for Docker container registries, these scripts handle the complexity of token refresh and credential management.

**Key Benefits:**
- ⚡ **Automated token refresh** - Never manually refresh NuGet or Docker credentials again
- 🔐 **AWS SSO integration** - Seamlessly work with AWS Single Sign-On profiles
- 🌐 **Cross-platform** - Works on Windows, macOS, and Linux via PowerShell Core
- 📝 **Detailed logging** - Comprehensive logs for auditing and troubleshooting
- 🔧 **Modular design** - Reusable modules for AWS CLI helpers and logging

---

## ✨ Features

- ✅ **AWS CodeArtifact Authentication** - Automatically refresh NuGet tokens for your CodeArtifact repositories
- ✅ **Amazon ECR Authentication** - Log Docker into ECR registries with a single command
- 🔑 **SSO Management** - Built-in `-SSO` switch to force AWS SSO login when sessions expire
- 🌍 **Region Flexibility** - Configure fallback regions or use detected AWS CLI settings
- 📊 **Debug Mode** - Verbose output and detailed logging for troubleshooting
- 🚀 **Automation Ready** - Easily integrate with cron jobs, Task Scheduler, or CI/CD pipelines

---

## 📦 Prerequisites

Before using these scripts, ensure you have the following installed and configured:

| Requirement | Description | Installation Guide |
|------------|-------------|-------------------|
| **AWS CLI v2** | Required for AWS authentication and SSO | [Installation Guide](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) |
| **PowerShell Core 7.x+** | Cross-platform PowerShell execution environment | [Installation Guide](https://docs.microsoft.com/en-us/powershell/scripting/install/installing-powershell) |
| **Docker** | Required for ECR authentication (Docker daemon must be running) | [Get Docker](https://docs.docker.com/get-docker/) |
| **AWS SSO Profile** | Configured SSO profile in your AWS CLI | [SSO Configuration](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) |

**IAM Permissions:**
- CodeArtifact: `codeartifact:*` permissions
- ECR: `ecr:*` permissions
- See [Required IAM Roles](Required%20IAM%20Roles.md) for minimal security-focused permissions

---

## 💿 Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/karimz1/AWS-Authentication-PWSH.git
   cd AWS-Authentication-PWSH
   ```

2. **Verify PowerShell Core installation:**
   ```bash
   pwsh --version
   ```

3. **Verify AWS CLI configuration:**
   ```bash
   aws configure list
   aws sso login --profile <your-sso-profile>
   ```

4. **Make scripts executable (Linux/macOS):**
   ```bash
   chmod +x *.ps1
   ```

---

## 🚀 Quick Start

**Refresh NuGet tokens and Docker credentials in one go:**

```powershell
# First time or when session expires
pwsh ./refreshNugetToken.ps1 -SSO
pwsh ./refreshEcrDockerToken.ps1 -SSO

# Subsequent runs with active session
pwsh ./refreshNugetToken.ps1
pwsh ./refreshEcrDockerToken.ps1
```

**That's it!** Your NuGet and Docker credentials are now refreshed and ready to use.

---

## 📖 Usage

All scripts should be executed from PowerShell Core (`pwsh`). Use the `-SSO` switch when your AWS session expires or you need to force a fresh login.

### 1. Refresh NuGet Tokens for AWS CodeArtifact

Authenticates with your AWS CodeArtifact domain and updates your NuGet configuration with fresh authentication tokens.

**Script:** `refreshNugetToken.ps1`

| Command | Purpose |
|:--------|:--------|
| `./refreshNugetToken.ps1` | **Standard Run** - Uses existing AWS session/profile |
| `./refreshNugetToken.ps1 -SSO` | **Force Login** - Executes `aws sso login` before refreshing tokens. Use when session expires |
| `./refreshNugetToken.ps1 -RegionFallback "eu-west-1"` | Specify fallback region if AWS CLI region is not configured |
| `./refreshNugetToken.ps1 -DEBUG $true` | Enable debug mode for verbose output and detailed logs |

**Example:**
```powershell
# Force SSO login and refresh tokens with debug output
pwsh ./refreshNugetToken.ps1 -SSO -DEBUG $true
```

---

### 2. Authenticate Docker with AWS ECR

Retrieves a temporary authentication token from Amazon ECR and logs your local Docker client into the registry.

**Script:** `refreshEcrDockerToken.ps1`

| Command | Purpose |
|:--------|:--------|
| `./refreshEcrDockerToken.ps1` | **Standard Run** - Uses existing AWS session/profile |
| `./refreshEcrDockerToken.ps1 -SSO` | **Force Login** - Executes `aws sso login` before fetching ECR credentials. Use when session expires |
| `./refreshEcrDockerToken.ps1 -RegionFallback "us-east-1"` | Specify fallback region (e.g., `us-east-1`) |
| `./refreshEcrDockerToken.ps1 -AccountId 123456789012` | Provide the 12-digit AWS Account ID directly, skipping `sts:GetCallerIdentity` |

**Example:**
```powershell
# Refresh ECR credentials for a specific account and region
pwsh ./refreshEcrDockerToken.ps1 -SSO -AccountId 123456789012 -RegionFallback "us-west-2"
```

---

## ⏰ Automate Session Refresh

Maintain continuous access without manual intervention by integrating these scripts into your system's scheduling tools.

### 🐧 Cron Job (Linux/macOS)

Run scripts automatically at system startup to ensure fresh sessions.

1. **Open crontab editor:**
   ```bash
   crontab -e
   ```

2. **Add the following lines** (adjust paths to match your setup):
   ```bash
   # Refresh AWS tokens at system startup/reboot
   @reboot pwsh /path/to/AWS-Authentication-PWSH/refreshNugetToken.ps1 -SSO
   @reboot pwsh /path/to/AWS-Authentication-PWSH/refreshEcrDockerToken.ps1 -SSO
   ```

### 🪟 Task Scheduler (Windows)

Use Windows Task Scheduler to run scripts automatically at user login.

1. **Open Task Scheduler** and create a new task
2. **Set Trigger:** "At log on"
3. **Set Action:** "Start a program"

| Script | Program/script | Add arguments |
|:-------|:---------------|:--------------|
| **NuGet** | `pwsh` | `-NoProfile -File "C:\path\to\AWS-Authentication-PWSH\refreshNugetToken.ps1" -SSO` |
| **ECR** | `pwsh` | `-NoProfile -File "C:\path\to\AWS-Authentication-PWSH\refreshEcrDockerToken.ps1" -SSO` |

---

## 🔐 IAM Permissions

For detailed IAM roles and minimal security-focused permissions, see:

📄 **[Required IAM Roles Documentation](Required%20IAM%20Roles.md)**

This document includes JSON policy examples for:
- CodeArtifact domain listing and authentication
- ECR pull permissions and authorization tokens
- STS caller identity verification

---

## 🔧 Troubleshooting

### Common Issues

**❌ "aws sso login failed"**
- Ensure your SSO profile is correctly configured: `aws configure sso`
- Check your SSO start URL and region settings

**❌ "Docker daemon is not running"**
- Start Docker Desktop or Docker service
- Verify with: `docker info`

**❌ "Unable to locate credentials"**
- Run with `-SSO` flag to force fresh login
- Verify AWS CLI configuration: `aws configure list`

**❌ "Region not configured"**
- Use `-RegionFallback` parameter to specify a region
- Configure default region: `aws configure set region us-east-1`

### Debug Mode

Enable detailed logging for troubleshooting:
```powershell
pwsh ./refreshNugetToken.ps1 -DEBUG $true
```

---

## 🤝 Contributing

Contributions are very welcome! Whether you have bug fixes, feature ideas, or documentation improvements, I'd love to collaborate.

**How to contribute:**
1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

Please read [CONTRIBUTING.md](CONTRIBUTING.md) for detailed guidelines.

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

## 💖 Support

If you find these scripts helpful, consider:
- ⭐ **Starring this repository** to help others discover it
- 💬 **Sharing feedback** through issues or discussions
- 💝 **[Sponsoring on GitHub](https://github.com/sponsors/karimz1)** to support continued development

---

<div align="center">

**Made with ❤️ by [Karim Zouine](https://github.com/karimz1)**

[Report Bug](https://github.com/karimz1/AWS-Authentication-PWSH/issues) · [Request Feature](https://github.com/karimz1/AWS-Authentication-PWSH/issues) · [Discussions](https://github.com/karimz1/AWS-Authentication-PWSH/discussions)

</div>
