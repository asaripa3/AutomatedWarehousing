# Automated Warehouse Planning with ASP

## Installation Guide

1. **Install Clingo** (e.g. `pip install clingo` or via your package manager).
2. Clone the Repository.
3. **Run** the solver on an instance:

   ```bash
   clingo domain.txt SimpleInstances/inst1.txt -c max=13
