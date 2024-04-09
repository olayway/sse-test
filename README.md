# Our Roadmap

<VegaLite
  spec={{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "description": "a serpentine timeline. The serpentine shape can be an option for instances where an oblong canvas is not ideal. The shape can be customized using many of the signals below. Input bindings have been included for demonstration purposes",
  "padding": 15,
  "width": 300,
  "scales": [
    {
      "name": "sS1",
      "type": "linear",
      "zero": false,
      "reverse": { "signal": "reverse" },
      "domain": { "signal": "sDomain" },
      "range": { "signal": "sRange" }
    },
    {
      "name": "footerY",
      "type": "band",
      "domain": { "data": "footer", "field": "id" },
      "range": [
        { "signal": "height+60" },
        { "signal": "height+60+length(data('footer'))*11" }
      ]
    }
  ],
  "marks": [
    {
      "name": "axis_group",
      "description": "group containing all the axis marks - annotations, domain, arrow indicators, tick lines, tick labels on straightaways, tick labels on arcs",
      "type": "group",
      "marks": [
        {
          "name": "annotations",
          "description": "Text marks that appear at the start and end of the timeline. Configured using the 'annotations' signal",
          "from": { "data": "domain_extent" },
          "on": [{ "trigger": "annotations", "modify": "annotations" }],
          "type": "text",
          "interactive": false,
          "encode": {
            "update": {
              "x": { "field": "x" },
              "y": { "field": "y" },
              "text": {
                "signal": "datum['category'] === 'start' ? annotations['start'] : annotations['end']"
              },
              "fontSize": { "value": 10 },
              "baseline": { "value": "middle" },
              "align": { "field": "align" },
              "angle": { "field": "angle" },
              "dx": { "field": "dx" },
              "dy": { "signal": "1" },
              "fill": { "value": "gray" }
            }
          }
        },
        {
          "name": "serpentine_line",
          "description": "The serpentine-shaped line that acts as the axis domain line",
          "type": "line",
          "from": { "data": "serpentine" },
          "interactive": false,
          "encode": {
            "update": {
              "x": { "field": "x" },
              "y": { "field": "y" },
              "strokeDash": { "signal": "sD" },
              "stroke": { "value": "#888" },
              "strokeWidth": { "signal": "sT" }
            }
          }
        },
        {
          "name": "arrow_marks",
          "description": "The arrows to indicate direction that appear at the beginning and end of each arc+straightaway combonation",
          "type": "text",
          "from": { "data": "segment_ends" },
          "interactive": false,
          "encode": {
            "update": {
              "x": { "field": "x" },
              "y": { "field": "y" },
              "dy": { "value": 1 },
              "text": { "value": "➤" },
              "fontSize": { "signal": "18" },
              "fill": { "value": "#000" },
              "stroke": { "value": "#fff" },
              "strokeWidth": { "value": 1 },
              "angle": { "signal": "datum['direction'] === '→' ? 0 : 180" },
              "align": { "value": "center" },
              "baseline": { "value": "middle" }
            }
          }
        },
        {
          "name": "tick_marks",
          "description": "The line (text mark) designated to each tick",
          "from": { "data": "ticks" },
          "type": "text",
          "interactive": false,
          "encode": {
            "update": {
              "x": { "field": "x" },
              "y": { "field": "y" },
              "dy": {
                "signal": "2.5*( datum['type'] === 'straight' ? 1 : datum['side'] === 'right' ? (round(datum['alpha']*(180/PI)) >= 90 ? -1 : 4) : (round(datum['alpha']*(180/PI)) > 89 ? -1 : 4))"
              },
              "text": { "signal": "'|'" },
              "fontSize": { "signal": "7" },
              "fill": { "value": "#000" },
              "angle": { "field": "labelAngle" },
              "align": { "value": "center" },
              "baseline": {
                "signal": "datum['type'] === 'straight' ? 'top' : 'bottom'"
              }
            }
          }
        },
        {
          "name": "tick_labels_straight",
          "description": "The straightaway tick labels",
          "from": { "data": "ticks" },
          "type": "text",
          "interactive": false,
          "encode": {
            "update": {
              "x": { "field": "x" },
              "y": { "field": "y" },
              "dy": { "field": "dy" },
              "text": { "field": "domain" },
              "fontSize": { "signal": "10" },
              "fill": { "value": "#000" },
              "align": { "value": "center" },
              "angle": { "field": "labelAngle" },
              "baseline": {
                "signal": "datum['type'] === 'straight' ? 'top' : 'bottom'"
              }
            }
          }
        }
      ]
    },
    {
      "name": "milestone_connecting_lines",
      "description": "The milestone lines that connect the markers to labels",
      "from": { "data": "milestones" },
      "type": "text",
      "interactive": false,
      "encode": {
        "update": {
          "text": { "signal": "'|'" },
          "x": { "field": "x" },
          "y": { "field": "y" },
          "fontSize": { "value": 15 },
          "fontWeight": { "value": 100 },
          "dy": {
            "signal": "datum['type'] === 'arc' && (round(datum['alpha']*(180/PI)) > 89) ? 0.35 * (mO+3.5) : datum['dy']/2"
          },
          "align": { "value": "center" },
          "baseline": { "value": "middle" },
          "angle": { "field": "labelAngle" },
          "fillOpacity": { "value": 0.35 },
          "opacity": {
            "signal": "isValid(hoverFocus) && datum['label'] === hoverFocus['label'] ? 1 : labelsOnHover ? 0 : 1"
          }
        }
      }
    },
    {
      "name": "milestone_markers",
      "description": "The milestone timeline markers",
      "from": { "data": "milestones" },
      "type": "symbol",
      "interactive": true,
      "encode": {
        "update": {
          "x": { "field": "x" },
          "y": { "field": "y" },
          "size": {
            "signal": "labelsOnHover && isValid(hoverFocus) && datum['label'] === hoverFocus['label'] ? 200 : 150"
          },
          "fill": {
            "signal": "labelsOnHover ? isValid(hoverFocus) && datum['label'] === hoverFocus['label'] ? 'firebrick' : '#e4b3b4' : 'firebrick'"
          },
          "stroke": {
            "signal": "labelsOnHover ? isValid(hoverFocus) && datum['label'] === hoverFocus['label'] ? '#fff' : 'firebrick' : '#fff'"
          },
          "strokeWidth": { "signal": "labelsOnHover ? 1 : 2" },
          "cursor": { "signal": "labelsOnHover ? 'pointer' : 'default'" }
        }
      }
    },
    {
      "name": "milestone_label_backgrounds",
      "description": "The white backgrounds for milestone labels",
      "from": { "data": "milestones" },
      "type": "text",
      "interactive": false,
      "encode": {
        "update": {
          "x": { "field": "x" },
          "y": { "field": "y" },
          "dy": { "field": "dy" },
          "text": { "signal": "datum['domain'] + ' - ' + datum['label']" },
          "fontSize": { "signal": "10" },
          "fill": { "value": "#fff" },
          "stroke": { "value": "#fff" },
          "strokeWidth": { "value": 7 },
          "angle": { "field": "labelAngle" },
          "align": { "value": "center" },
          "baseline": {
            "signal": "datum['type'] === 'straight' ? 'top' : 'bottom'"
          },
          "opacity": {
            "signal": "isValid(hoverFocus) && datum['label'] === hoverFocus['label'] ? 1 : labelsOnHover ? 0 : 1"
          }
        }
      }
    },
    {
      "name": "milestone_labels",
      "description": "The milestone labels",
      "from": { "data": "milestones" },
      "type": "text",
      "interactive": false,
      "encode": {
        "update": {
          "x": { "field": "x" },
          "y": { "field": "y" },
          "dy": { "field": "dy" },
          "text": { "signal": "datum['domain'] + ' - ' + datum['label']" },
          "fontSize": { "signal": "10" },
          "fill": { "value": "firebrick" },
          "angle": { "field": "labelAngle" },
          "align": { "value": "center" },
          "baseline": {
            "signal": "datum['type'] === 'straight' ? 'top' : 'bottom'"
          },
          "opacity": {
            "signal": "isValid(hoverFocus) && datum['label'] === hoverFocus['label'] ? 1 : labelsOnHover ? 0 : 1"
          }
        }
      }
    }
  ],
  "data": [
    {
      "name": "dataset",
      "values": [
        { "domain": 1928, "label": "Major Event A" },
        { "domain": 1939, "label": "Major Event B" },
        { "domain": 1954, "label": "Major Event C" },
        { "domain": 1962, "label": "Major Event D" },
        { "domain": 1974, "label": "Major Event E" },
        { "domain": 1990, "label": "Major Event F" },
        { "domain": 2001, "label": "Major Event G" },
        { "domain": 2015, "label": "Major Event H" }
      ]
    },
    {
      "name": "serpentineDomain",
      "values": [{}],
      "transform": [
        {
          "type": "formula",
          "expr": "sequence(sDomain[0],sDomain[1], 0.1 )",
          "as": "domain"
        },
        { "type": "flatten", "fields": ["domain"] }
      ]
    },
    {
      "name": "milestoneDomain",
      "source": "dataset",
      "transform": [{ "type": "project", "fields": ["domain"] }]
    },
    {
      "name": "tickDomain",
      "values": [{}],
      "transform": [
        { "type": "formula", "expr": "sequence(1,tC+1, 1)", "as": "id" },
        { "type": "flatten", "fields": ["id"] },
        {
          "type": "formula",
          "expr": "datum['id'] === 1 ? sDomain[0] : datum['id'] === tC ? sDomain[1] : null",
          "as": "domain"
        },
        {
          "type": "formula",
          "expr": "round(isValid(datum['domain']) ? datum['domain'] : (sDomain[0] + (sDomain[1]-sDomain[0])*((datum['id']-1)/(tC-1))))",
          "as": "domain"
        },
        { "type": "project", "fields": ["domain"] }
      ]
    },
    {
      "name": "componentEncodings",
      "values": [
        { "category": "start" },
        { "category": "serpentine" },
        { "category": "milestone" },
        { "category": "tick" },
        { "category": "end" }
      ],
      "transform": [
        { "type": "formula", "expr": "now()", "as": "timestamp" },
        {
          "type": "formula",
          "expr": "datum['category'] === 'start' ? [sDomain[reverse ? 1 : 0]] : datum['category'] === 'serpentine' ? pluck(data('serpentineDomain'), 'domain') : datum['category'] === 'milestone' ? pluck(data('dataset'), 'domain') : datum['category']==='tick' ? pluck(data('tickDomain'), 'domain') : datum['category'] === 'end' ? [sDomain[reverse ? 0 : 1]] : null",
          "as": "domain"
        },
        { "type": "flatten", "fields": ["domain"] },
        { "type": "formula", "expr": "+datum['domain']", "as": "domain" },
        {
          "type": "window",
          "ops": ["row_number"],
          "sort": { "field": "domain" },
          "groupby": ["category"],
          "as": ["id"]
        },
        {
          "type": "formula",
          "expr": "scale('sS1', datum['domain'])",
          "as": "sK"
        },
        {
          "type": "formula",
          "expr": "floor(datum['sK'] / (sW + sH*PI/2))",
          "as": "i"
        },
        {
          "type": "formula",
          "expr": "datum['sK'] % (sW + sH*PI/2)",
          "as": "r"
        },
        {
          "type": "formula",
          "expr": "(datum['r'] - sW)/(sH/2)",
          "as": "alpha"
        },
        {
          "type": "formula",
          "expr": "(((datum['i']+1)*sWsA)-sA) >= datum['sK'] ? 'straight' : 'arc'",
          "as": "type"
        },
        {
          "type": "formula",
          "expr": "(datum['i']%2 == 0) ? min(datum['r'],sW) : max(sW-datum['r'], 0)",
          "as": "xStraight"
        },
        {
          "type": "formula",
          "expr": "datum['type'] === 'straight' ? datum['xStraight'] : datum['xStraight'] + (datum['i']%2 == 0 ? sin(datum['alpha'])*sH/2 : -sin(datum['alpha'])*sH/2)",
          "as": "x"
        },
        {
          "type": "formula",
          "expr": "datum['type'] === 'straight' ? datum['i']*sH : (datum['i']*sH) + (1 - cos(datum['alpha']))*sH/2",
          "as": "y"
        },
        {
          "type": "formula",
          "expr": "datum['type'] === 'straight' ? null : datum['i']%2===0?'right':'left'",
          "as": "side"
        },
        {
          "type": "formula",
          "expr": "datum['type'] === 'straight' ? null : datum['alpha']<PI/2?'top':'bottom'",
          "as": "hemisphere"
        },
        {
          "type": "formula",
          "expr": "datum['type'] === 'straight' ? 0 : (datum['side'] === 'left' ? -1 : 1) *datum['alpha']*(180/PI)+(datum['alpha'] < PI/2 ? 0 : 180)",
          "as": "labelAngle"
        },
        {
          "type": "formula",
          "expr": "datum['i']%2===0 ?'→':'←'",
          "as": "direction"
        },
        {
          "type": "formula",
          "expr": "datum['type'] === 'straight' ? datum['direction'] : datum['side'] === 'left' ? datum['hemisphere'] === 'top'? '←' : '→' :  datum['hemisphere'] === 'top'? '→' : '←'",
          "as": "direction"
        }
      ]
    },
    {
      "name": "serpentine",
      "source": "componentEncodings",
      "transform": [
        { "type": "filter", "expr": "datum['category'] === 'serpentine'" }
      ]
    },
    {
      "name": "ticks",
      "source": "componentEncodings",
      "transform": [
        { "type": "filter", "expr": "datum['category'] === 'tick'" },
        {
          "type": "formula",
          "expr": "!isValid(datum['side']) ? (tLO+3.5) : datum['side'] === 'right' ? (round(datum['alpha']*(180/PI)) >= 90 ? -1 : 1.75) * (tLO+3.5) : (round(datum['alpha']*(180/PI)) > 89 ? -1 : 1.75) * (tLO+3.5)",
          "as": "dy"
        }
      ]
    },
    {
      "name": "domain_extent",
      "source": "componentEncodings",
      "transform": [
        {
          "type": "filter",
          "expr": "datum['category'] === 'start' || datum['category'] === 'end'"
        },
        {
          "type": "formula",
          "expr": "datum['category'] === 'start' ? 'right' : datum['direction'] === '←' && datum['type'] === 'straight' ? 'right' : datum['side'] === 'left' ? 'right' : 'left'",
          "as": "align"
        },
        {
          "type": "formula",
          "expr": "datum['category'] === 'start' ? -tLO-5 :  datum['direction'] === '←'  && datum['type'] === 'straight' ? -tLO-5 : datum['side'] === 'left' ? -tLO-5 : tLO+5",
          "as": "dx"
        }
      ]
    },
    {
      "name": "segment_ends",
      "source": "componentEncodings",
      "transform": [
        {
          "type": "filter",
          "expr": "includeArrows && datum['category'] === 'serpentine'"
        },
        {
          "type": "joinaggregate",
          "fields": ["id", "id"],
          "ops": ["min", "max"],
          "groupby": ["i"],
          "as": ["minId", "maxId"]
        },
        {
          "type": "filter",
          "expr": "datum['id'] === (reverse ? datum['maxId'] : datum['minId'])"
        }
      ]
    },
    {
      "name": "footer",
      "values": [
        {
          "id": 1,
          "text": [
            "Data Source:",
            "https://ourworldindata.org/mass-extinctions"
          ],
          "href": "https://ourworldindata.org/mass-extinctions"
        },
        {
          "id": 2,
          "text": ["Icons:", "https://fontawesome.com/"],
          "href": "https://fontawesome.com/"
        },
        {
          "id": 3,
          "text": ["Data Viz By:", "Madison Giammaria"],
          "href": "https://www.linkedin.com/in/madison-giammaria-58463b33"
        }
      ]
    },
    {
      "name": "milestones",
      "source": "componentEncodings",
      "transform": [
        { "type": "filter", "expr": "datum['category'] === 'milestone'" },
        {
          "type": "lookup",
          "key": "domain",
          "from": "dataset",
          "fields": ["domain"],
          "values": ["label", "color"]
        },
        {
          "type": "formula",
          "expr": "!isValid(datum['side']) ? -(mO) : datum['side'] === 'right' ? (round(datum['alpha']*(180/PI)) >= 90 ? 1 : -0.5) * (mO+3.5) : (round(datum['alpha']*(180/PI)) > 89 ? 1 : -0.5) * (mO+3.5)",
          "as": "dy"
        },
        {
          "type": "formula",
          "expr": "(isValid(hoverFocus) && hoverFocus['domain']===datum['domain']) ? 1 : 0.4",
          "as": "fillOpacity"
        }
      ]
    }
  ]
}}
/>
