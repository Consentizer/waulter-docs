---
title: Account Registration & Sharing
description: >
  Registering client user accounts, sharing configurations (local vs global share),
  roles (read vs admin), and how client personnel access their shared configs.
---

# Account Registration & Sharing

Waulter's sharing system lets agencies grant client personnel access to their consent configurations — from view-only monitoring to full administrative control.

## Sharing types

Waulter supports two types of sharing, designed for different use cases:

### Local share (per-configuration)

Share a **single, specific configuration** with a specific user.

| Property | Detail |
|----------|--------|
| **Scope** | One configuration |
| **Use case** | Give a client access to only their own configuration |
| **Cascade** | Automatically shares tied scenarios as well |
| **Persistence** | Until explicitly removed |

**When to use:** When a client should see only their own configuration and nothing else.

### Global share (per-partner)

Share **all configurations under a partner** with a specific user. This is a persistent rule — the user automatically gains access to any new configurations created under that partner in the future.

| Property | Detail |
|----------|--------|
| **Scope** | All configurations under a partner (current and future) |
| **Use case** | Agency team members who need access to all client configs |
| **Cascade** | Automatically includes all configurations and their tied scenarios |
| **Persistence** | Persistent rule — current and future configurations |

**When to use:** When an agency team member needs access to everything under a partner, or when a client has multiple configurations and should see all of them.

## Roles

| Role | Permissions | Best for |
|------|-------------|----------|
| **Read** | View configuration settings, statistics, compliance reports. Cannot modify anything. | Client personnel who need to monitor consent metrics but should not change settings |
| **Admin** | Full control: view, edit, activate/deactivate, manage texts, purposes, styling, and re-share with others | Client technical teams who manage their own consent setup |

!!! tip "Start with read access"
    When onboarding a new client contact, start with **read** access. Upgrade to **admin** if and when they need to make changes. This prevents accidental modifications.

## How to share a configuration

### Local share (from configuration detail)

1. Open the configuration in the dashboard.
2. Navigate to the **Sharing** section.
3. Enter the client user's email address.
4. Select the role (**admin** or **read**).
5. Click **Share**.

The user receives access immediately. Tied scenarios are automatically shared as well.

### Global share (from partner detail)

1. Navigate to the partner in the dashboard.
2. Open the **Global Sharing** section.
3. Enter the user's email address.
4. Select the role.
5. Click **Add Global Share**.

The user immediately gains access to all configurations under this partner, including any created in the future.

## How client personnel access shared configs

1. The client logs in to the Waulter B2B dashboard with their credentials.
2. Shared configurations appear in their configuration list.
3. They can only see configurations that have been shared with them (local or global).
4. Their available actions are determined by their role (read or admin).

### What clients see

| Element | Read role | Admin role |
|---------|-----------|-----------|
| Configuration settings | View only | View and edit |
| Statistics | View | View |
| Compliance reports | View | View |
| Texts & translations | View only | Edit |
| Purposes | View only | Edit |
| Styling | View only | Edit |
| Activate / Deactivate | No | Yes |
| Share with others | No | Yes |

## Managing access

### Viewing who has access

Open the configuration's **Sharing** section to see all users with access:

| Column | Description |
|--------|-------------|
| **Name** | User's display name |
| **Email** | User's email address |
| **Access type** | `local` (shared directly), `global` (via partner), or `local + global` (both) |
| **Role** | `admin` or `read` |

### Removing access

- **Local shares**: Click **Remove** next to the user in the configuration sharing section.
- **Global shares**: Remove from the partner's **Global Sharing** section. This revokes access to all configurations under the partner.

!!! warning "Self-removal"
    Users can remove themselves from a shared configuration. After self-removal, the configuration disappears from their dashboard immediately. This action cannot be undone from the user's side — the configuration owner or an admin must re-share.

## Registering new client users

Before you can share a configuration, the client must have a registered Waulter account:

1. The client registers at the Waulter sign-up page.
2. They receive a confirmation email and activate their account.
3. Once registered, you can share configurations with them using their email address.

## Inviting additional team members

To add more people from a client organisation:

1. Each person registers their own Waulter account.
2. Share the configuration (local) or add them to the global share (global) using their email.
3. Each person logs in independently and sees the shared configurations.

### Revoking access when team members leave

When a client contact leaves their organisation:

1. Remove their local share from each configuration, or
2. Remove their global share from the partner, or
3. The user can self-remove if they are cleaning up their own access.

!!! tip "Regular access reviews"
    Periodically review who has access to your configurations. Remove users who no longer need access — this is a good practice for both security and compliance.
