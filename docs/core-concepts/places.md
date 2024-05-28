---
title: Places
sidebar_position: 3
slug: /core-concepts/places
---

## Overview

Places are a foundational resource in Fleetbase, defining physical locations with detailed attributes for comprehensive geographic referencing.

## Structure of an Place

- **Name, Address Details**: Includes primary and secondary address lines, postal code, province/state, city, and country.
- **Geographical Coordinates**: Locations can be defined using MySQL POINT or GeoJSON formats.
- **Contact Information**: Places may include associated phone numbers.

## Associations

- **Contacts and Vendors**: Places can be linked to contacts or vendors, enhancing the utility of location data across various modules.
- **Module Integration**: Used by the inventory module to define warehouses and by the storefront module for store locations.