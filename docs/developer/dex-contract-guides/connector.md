---
title: Connector
parent: DEX Contract Guides
nav_order: 3
---

## Overview
The Connector module manages the list of connector tokens and their required minimum amounts for pool creation and routing.

## Views (read-only)

### get_connectors
```
public fun get_connectors(): (vector<Object<Metadata>>, vector<u128>)
```
Description: Returns the current list of connector tokens and their corresponding minimum required amounts.

| Returns | Type | Description |
|---------|------|-------------|
| tokens | `vector<Object<Metadata>>` | Connector token metadata list |
| amounts | `vector<u128>` | Minimum required amounts aligned by index |
