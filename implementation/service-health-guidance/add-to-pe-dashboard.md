# Add a new charm to Platform Engineering dashboard

## Submit a PR

Submit a PR to add a Grafana dashboard named "Charm's Name Service Health."

See how to create one in [Create Dashboard](create-dashboard).

## Access COS

Once approved and deployed, access it in Grafana.

## Create library panels

For each panel:
   - Click on the three dots (⋮) → More → Create library panel.
   - Name the library panel following the pattern [Charm's Name][Signal Name] (e.g., [Synapse][LATENCY]).
   - Save it in the "Service Health Status" folder.

## Add a row

1. Go to the dashboards list and open "Platform Engineering Services Health" in the "Service Health Status" folder.
2. Click "Add" → Add a row.
3. Click "Add" again → Import from library.
4. Import the panels you previously added as library panels.
5. Adjust their size to fit within the row.

## Create dashboard variable

Open Dashboard Settings (⚙️) → Variables.

Add a new variable:
    - Name: `charmsname_model`
    - Type: Query
    - Show on dashboard: Label and value
    - Query Type: Label values
    - Label: `juju_model`
    - Data Source: `${prometheusds}`
    - Sort: Alphabetical (ASC)

## Update the row title
 - Click on the row settings wheel (⚙️) in the row header.
 - Rename it to "Charm's Name [$charmsname_model]".

