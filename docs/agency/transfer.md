---
title: Transfer of Ownership
description: >
  When and why to transfer configuration ownership from agency to client,
  self-removal from shared configs, and step-by-step handoff process.
---

# Transfer of Ownership

As client relationships evolve, agencies may need to transfer full ownership of Waulter configurations to the client. This page covers when, why, and how to perform a clean handoff.

## When to transfer ownership

| Situation | Action needed |
|-----------|--------------|
| Client is bringing consent management in-house | Full ownership transfer |
| Agency engagement is ending | Transfer before contract ends |
| Client has hired their own technical team | Transfer to the client's lead |
| Regulatory requirement | Client must directly control their consent infrastructure |
| Client switching agencies | Transfer to client, then new agency re-shares |

## Why transfer matters

- **Ownership = ultimate control** — the owner can manage sharing, activate/deactivate, and delete the configuration
- **Clean boundaries** — prevents confusion about who is responsible for the configuration
- **Compliance** — some regulations may require the data controller (client) to directly manage consent infrastructure
- **Continuity** — ensures the client has full access even if the agency relationship ends

## Step-by-step handoff process

### 1. Prepare the client

Ensure the client has:

- [x] An active Waulter account (registered and verified)
- [x] Understanding of the Waulter dashboard basics
- [x] Their Configuration ID for reference
- [x] Access to the GTM container or website code where the SDK is deployed

### 2. Share with admin access

If not already shared, share the configuration with the client's designated admin:

1. Open the configuration > **Sharing** section.
2. Add the client's user with **admin** role.
3. Verify the client can log in and see the configuration.

### 3. Document the configuration

Before transfer, provide the client with:

| Item | Details |
|------|---------|
| **Configuration ID** | The ID used in their SDK deployment |
| **Scenario ID** (if applicable) | The scenario ID used in their GTM/script setup |
| **Whitelisted domains** | List of all domains currently whitelisted |
| **GCM mode** | Basic or Advanced |
| **Active purposes** | List of configured purpose categories |
| **Linked documents** | Cookie Policy and Privacy Policy document IDs |
| **Custom fields** | Any custom fields used in scenarios |

### 4. Transfer ownership

!!! info "Ownership transfer process"
    Ownership transfer may require administrative action in the dashboard. Contact Waulter support if the dashboard does not provide a direct transfer option.

### 5. Verify the transfer

After transfer:

- The client should be listed as the configuration **owner** in the dashboard
- The client should have full admin control
- The client can manage sharing, activate/deactivate, and modify all settings

### 6. Agency self-removal (optional)

After the transfer is complete, the agency can remove their own access:

- **Keep access** if there is an ongoing support agreement
- **Remove access** for a clean break

!!! warning "Self-removal is immediate and irreversible"
    When you remove yourself from a configuration, you lose access immediately. The configuration disappears from your dashboard. Only the new owner or an admin can re-share with you.

## What happens after transfer

| Aspect | Result |
|--------|--------|
| **Configuration settings** | Preserved — nothing changes |
| **Statistics** | Preserved — historical data belongs to the configuration |
| **Consent records** | Preserved — all visitor consent is maintained |
| **Existing shares** | Preserved — other users' access is not affected |
| **Scenarios** | Preserved — continue to work as configured |
| **SDK deployment** | No change needed — the Configuration ID remains the same |

## Best practices for clean transitions

1. **Set a transition date** — communicate it to all stakeholders in advance
2. **Provide documentation** — give the client links to relevant Waulter documentation
3. **Offer a transition period** — keep shared access for a defined period (e.g. 30 days) in case the client needs help
4. **Verify independently** — ask the client to log in and confirm they can manage the configuration before you remove your access
5. **Update your records** — mark the configuration as transferred in your agency's tracking system
