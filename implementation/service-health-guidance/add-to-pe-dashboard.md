# Add a new charm to Platform Engineering dashboard

This how-to guide will walk you through how to add a new dashboard to the Platform Engineering dashboard.

Follow the instructions below if the charm is in production in PS6 and should be monitored by the Ninja.

Currently, the dashboard is only available for charms deployed in PS6.

## Submit a PR

Submit a PR to add a Grafana dashboard named "Charm's Name Service Health" to
the Charm GitHub repository (together with existing Grafana dashboards).

See how to create one in [Create Dashboard](create-dashboard).

## Access COS

Once approved and deployed, access it in Grafana.

## Create library panels

For each panel:

1. Click on the three dots (⋮) → More → Create library panel.
2. Name the library panel following the pattern [Charm's Name][Signal Name] (e.g., [Synapse][LATENCY]).
3. Save the panel in the "Service Health Status" folder.

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

Mind that data source can be Loki as well if your charm is integrated with it.

## Update the row title

1. Click on the row settings wheel (⚙️) in the row header.
2. Rename it to "Charm's Name [$charmsname_model]".

