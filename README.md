# Automated Warehouse Planning with ASP

This project simulates an automated warehouse environment, where intelligent robots navigate a grid-based warehouse to retrieve shelves and fulfill customer orders using **Answer Set Programming (ASP)**. The system models robot actions, warehouse constraints, and product delivery goals to generate optimized movement and task plans via the **Clingo** ASP solver.

## How to Run

To solve a warehouse instance:

```bash
clingo domain.txt SimpleInstances/inst1.txt -c max=13
```

## Project Overview

In modern fulfillment centers, robots like Amazon’s Kiva system are replacing human labor for tasks involving shelf handling, navigation, and delivery. This project explores how **declarative planning** using ASP can efficiently solve such complex logistical challenges by encoding the domain rules and goals and letting a solver derive optimal action sequences.

> **Objective**: Safely, efficiently, and autonomously fulfill all product orders by directing robots through a dynamic warehouse layout under traffic rules and physical constraints.

## Problem Setup

- Warehouse modeled as a **4x4 grid** of 16 nodes.
- Multiple entities:
  - **2 robots**
  - **6 shelves (racks)**
  - **4 products**
  - **2 picking stations**
  - **3 customer orders**
- Special zones:
  - Highways: nodes robots cannot park or place shelves on.
  - Picking stations: product delivery points.

Each problem instance encodes:
- Initial robot positions
- Shelf and product placements
- Product-to-order mappings
- Constraints like no-collision, rack handling rules, delivery requirements

## ASP-Based Solution

### Domain File (Logic Rules)
- **Actions**:
  - `robotMoveUp/Down/Left/Right`
  - `pickUp`, `putDown` for shelf manipulation
  - `deliver` for product delivery
- **Constraints**:
  - Robots can’t go outside bounds
  - No multiple robots/shelves per node
  - Cannot park on highways or picking stations
  - Shelf pickup only when at same node
- **Inertia**: Models persistence of facts over time unless changed.
- **Optimization**: Minimize total actions and time.

### Problem File (Instance Facts)
Encodes initial state, positions, shelf-product mapping, and delivery goals for each test instance (`inst1.txt` to `inst5.txt`).

## Results Summary

| Instance | Min Steps | Time Taken |
|----------|-----------|------------|
| inst1    | 13 steps  | 1.24s      |
| inst2    | 11 steps  | 0.26s      |
| inst3    | 7 steps   | 0.05s      |
| inst4    | 10 steps  | 0.36s      |
| inst5    | 6 steps   | 0.03s      |

- Robots cooperatively divide responsibilities.
- Tasks are completed respecting all constraints.
- Plans are generated in under 1 second using Clingo on a MacBook Air (M2 chip).

## Case Study: Instance 1

- Robot 1: Delivered Product 2 with vertical movements.
- Robot 2: Performed more dynamic actions, delivering Products 1 & 3.
- Coordination was based on task complexity and location.

**Timeline Snapshot:**

| Time | Robot 1 Action       | Robot 2 Action           |
|------|----------------------|--------------------------|
| 0    | Move Up              | Move Up                  |
| 1    | Move Up              | PickUp Shelf 6           |
| 2    | —                    | Move Right               |
| 3    | PickUp Shelf 3       | Deliver 4×Product 1      |
| 4    | Move Up              | Move Left                |
| 5    | —                    | PutDown Shelf            |
| 6    | Move Down            | Move Down                |
| 7    | PutDown Shelf 3      | Move Down                |
| 8    | Move Left            | PickUp Shelf 5           |
| 9    | PickUp Shelf 4       | Move Left                |
| 10   | Move Down            | Deliver 1×Product 3      |
| 11   | Move Left            | Move Down                |
| 12   | Deliver 1×Product 2  | —                        |
