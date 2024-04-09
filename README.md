# Our Roadmap

<VegaLite
  data={{
    "values": [
      {"milestone": "Project Kickoff", "date": "2024-01-01"},
      {"milestone": "Phase 1 Complete", "date": "2024-03-01"},
      {"milestone": "Phase 2 Complete", "date": "2024-06-01"},
      {"milestone": "Beta Release", "date": "2024-09-01"},
      {"milestone": "Final Release", "date": "2025-01-01"}
    ]
  }}
  spec={{
  "$schema": "https://vega.github.io/schema/vega-lite/v5.json",
  "description": "Business Roadmap with Milestones",
  "mark": "point",
  "encoding": {
    "x": {
      "field": "date",
      "type": "temporal",
      "axis": {"title": "Date"}
    },
    "y": {
      "field": "milestone",
      "type": "nominal",
      "axis": {"title": "Milestone"}
    },
    "tooltip": [
      {"field": "milestone", "type": "nominal"},
      {"field": "date", "type": "temporal", "title": "Date"}
    ]
  }
}}
/>
