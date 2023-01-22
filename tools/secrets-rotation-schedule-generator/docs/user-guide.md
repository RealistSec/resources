# User Guide – Secrets Rotation Schedule Generator

## Goal
Plan rotation cadence and overlap windows for different secret types.

## Steps
1. Open `index.html`.
2. Select **Secret type** and **Environment**.
3. Set **Overlap days** to keep old/new secrets valid simultaneously.
4. Click **Generate schedule** and follow the steps listed.

## Tips
- Use longer overlap in production to reduce risk.
- Schedule rotations ahead of expirations and revoke old secrets after overlap.