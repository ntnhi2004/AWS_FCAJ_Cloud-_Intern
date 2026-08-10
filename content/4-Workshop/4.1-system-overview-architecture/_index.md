---
title : "CloudMenu system overview and architecture"
date : 2024-01-01
weight : 1
chapter : false
pre : " <b> 4.1. </b> "
---

## 4.1 CloudMenu system overview and architecture

### Overview

CloudMenu is a digital ordering system that uses table-specific QR codes, allowing customers to access the menu and place orders directly from their smartphones without requiring physical menus or dedicated ordering devices.

Each table in the restaurant is assigned a unique QR code. When a customer scans the QR code, the system identifies the corresponding table and opens the appropriate menu. Customers can browse available dishes, select items, place orders, and track their order status.

The system consists of three main interfaces:

- Customer Interface: Allows customers to browse the menu, select dishes, and place orders.
- Kitchen Interface: Allows kitchen staff to receive, process, and update the status of customer orders.
- Dashboard: Allows restaurant managers to monitor orders, revenue, and system activity statistics.

### Architecture

![CloudMenu AWS architecture](/images/AWS_CloudMenu.png)