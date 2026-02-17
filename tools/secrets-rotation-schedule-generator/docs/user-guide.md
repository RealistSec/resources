# User Guide – Secrets Rotation Schedule Generator

## Overview
The Secrets Rotation Schedule Generator helps security engineers and developers plan safe credential rotations. By visualizing the overlap between old and new secrets, you can prevent downtime during cutovers and ensure robust rollback capabilities.

## How to Use

### 1. Configuration
Use the sidebar panel to define your constraints:
- **Secret Type**: Choose from predefined types (API Key, DB Password, TLS Cert, etc.). Each type has a recommended risk profile and default cadence.
- **Environment**: Select the target environment. Production environments trigger stricter risk scoring.
- **Compliance Framework**: Apply presets for standards like **PCI DSS** (90 days) or **NIST** (Risk-based).
- **Rotation Cadence**: How often the secret should be rotated. Shorter is generally safer.
- **Overlap Window**: The critical period where *both* the old and new secrets are valid. This ensures zero-downtime rotation.

### 2. Reading the Schedule
The tool generates two views:
- **Visual Timeline**: A Gantt-style chart showing three future rotation cycles.
  - **Green Bar**: The validity period of a single secret version.
  - **Amber/Overlap**: The transition period where two secrets exist simultaneously.
- **Execution Steps**: A chronological checklist of actions required for the next rotation.

### 3. Risk Analysis
The Risk Meter calculates a score (0-100) based on your inputs.
- **Low Risk (Green)**: Excellent hygiene. Frequent rotations, safe overlap.
- **Medium Risk (Amber)**: Standard industry practice.
- **High Risk (Red)**: Long lived secrets in critical environments. Consider shortening the cadence.

### 4. Exporting
Click **Export JSON** to download a machine-readable file containing your configuration and the projected schedule. This is useful for:
- Attaching to Jira tickets/GitHub issues.
- Storing as evidence for compliance audits.
- Piping into automation tools.

### 5. Offline Mode
This tool is a single HTML file. You can save it to a USB drive or local folder and run it on air-gapped machines without any loss of functionality.