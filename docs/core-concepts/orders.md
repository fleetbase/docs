---
title: Orders
sidebar_position: 1
slug: /core-concepts/orders
---

## Overview

In Fleetbase, an Order represents a core resource that orchestrates the logistics and tracking of items from initiation to delivery. It encapsulates all details necessary to manage and track the movement of goods, including destinations, items (payload), configurations, and interactions with various system entities.

## Structure of an Order

### Destinations

- **Pickup**: The starting point where items are collected.
- **Dropoff**: The endpoint where items are delivered.
- **Waypoints**: Intermediate stops included in the delivery route.

### Payload

- **Entities**: Represent the physical items being transported, such as containers, parcels, etc.

### Additional Configuration

- **Proof of Delivery (POD)**: Confirmation of delivery with evidence like signatures or photos.
- **Ad-hoc (Adhoc)**: Indicates that the order is available for immediate fulfillment within a specific vicinity, usually near the pickup location.
- **Dispatch**: Orders can be dispatched immediately upon creation unless scheduled for a later time.

### Scheduling

- **Scheduled Date**: Specifies the date and time an order is scheduled for dispatch. Upon reaching this scheduled time, the order will automatically be dispatched.

### Assignments

- **Driver Assigned**: Specifies the driver or delivery person assigned to the order.
- **Vehicle Assigned**: Details the specific vehicle used to complete the order.

## Order Configurations

Defines the workflow and rules governing the lifecycle of an order, ensuring operational consistency and efficiency.

<div style={{display: 'flex', textAlign: 'center', display: 'flex', alignItems: 'center', justifyContent: 'center'}}>
    <img src="/img/order-config-activity-example.png" style={{width: '100%'}} />
</div>

### Activities

- **Defined Activities**: Steps or events in an order’s lifecycle, from pickup to delivery.
- **Logic and Events**: Control transitions between activities, with events triggered as activities change, facilitating system interactions.
Custom Fields and Entities
- **Custom Fields**: Additional data points attached to an order. For example, for a haulage order, a custom field could specify the vessel and its ETA for container pickup.
- **Custom Entities**: Template-based entities that standardize the creation of new orders, enhancing the system's adaptability.

## Facilitator

Orders may be managed or executed by a facilitator, an external partner or a subcontracted entity, which broadens the operational network and fulfillment capabilities.

## Quoting System

Fleetbase dynamically generates service quotes based on the specifics of the order and predefined service rates.

### Service Rates

- **Service Rate Definitions**: Configured in the console to guide cost calculations for various order types and scenarios.

## Documentation and Communication

- **Files and Documents**: Attach important documentation directly to an order, such as shipping labels and customs forms.
- **Comments**: Discussion threads attached to each order facilitate collaborative communication among stakeholders.

## Tracking and Labels

- **Tracking Number**: Generated upon order creation, enabling all parties to monitor the order's progress.
- **Shipping Labels**: Generated based on the order details and shipping requirements.

## Key Events in Order Lifecycle
- **Created**: Marks the initiation of a new order.
- **Dispatched**: Indicates that the order has been sent out for delivery.
- **Completed**: Confirms the completion of the order and fulfillment of all conditions.