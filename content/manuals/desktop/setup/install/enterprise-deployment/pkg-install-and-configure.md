---
title: PKG 安装程序
description: Understand how to use the PKG 安装程序. Also explore additional configuration options.
keywords: pkg, mac, docker desktop, install, deploy, configure, admin, mdm
tags: [admin]
weight: 20
---

{{< summary-bar feature_name="PKG 安装程序" >}}

The PKG package supports various MDM (Mobile Device Management) solutions, making it ideal for bulk installations and eliminating the need for manual setups by individual users. With this package, IT administrators can ensure standardized, policy-driven installations of Docker Desktop, enhancing efficiency and software management across their organizations.

## Install interactively

1. In the [Docker Admin Console](http://admin.docker.com/), navigate to your organization.
2. Under **Docker Desktop**, select the **Deploy** page.
3. From the **macOS** tab, select the **Download PKG 安装程序** button.
4. Once downloaded, double-click `Docker.pkg` to run the installer.
5. Follow the instructions on the installation wizard to authorize the installer and proceed with the installation.
   - **Introduction**: Select **Continue**.
   - **License**: Review the license agreement and select **Agree**.
   - **Destination Select**: This step is optional. It is recommended that you keep the default installation destination (usually `Macintosh HD`). Select **Continue**.
   - **Installation Type**: Select **Install**.
   - **Installation**: Authenticate using your administrator password or Touch ID.
   - **Summary**: When the installation completes, select **Close**.

> [!NOTE]
>
> When installing Docker Desktop with the PKG, in-app updates are automatically disabled. This ensures organizations can maintain version consistency and prevent unapproved updates. For Docker Desktop installed with the `.dmg` installer, in-app updates remain supported.
>
> Docker Desktop notifies you when an update is available. To update Docker Desktop, download the latest installer from the Docker Admin Console. Navigate to the  **Deploy** page > under **Docker Desktop**.
>
> To keep up to date with new releases, check the [release notes](/manuals/desktop/release-notes.md) page.

## Install from the command line

1. In the [Docker Admin Console](http://admin.docker.com/), navigate to your organization.
2. Under **Security and access**, select the **Deploy Docker Desktop** page.
3. From the **macOS** tab, select the **Download PKG 安装程序** button.
4. From your terminal, run the following command:

   ```console
   $ sudo installer -pkg "/path/to/Docker.pkg" -target /Applications
   ```

## Additional resources

- See how you can deploy Docker Desktop for Mac using [Intune](use-intune.md) or [Jamf Pro](use-jamf-pro.md)
- Explore how to [Enforce sign-in](/manuals/security/for-admins/enforce-sign-in/methods.md#plist-method-mac-only) for your users.
