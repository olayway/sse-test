# 2024 Business Roadmap

Welcome to our comprehensive guide for our strategic business initiatives and key milestones planned for 2024. This document outlines our priorities, major project timelines, and the exciting opportunities we aim to capture.

<VegaLite
  spec={{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "description": "a serpentine timeline. The serpentine shape can be an option for instances where an oblong canvas is not ideal. The shape can be customized using many of the signals below. Input bindings have been included for demonstration purposes",
  "padding": 15,
  "width": 300,
  "signals": [
    {
      "name": "width",
      "init": "width"
    },
    {
      "name": "sH",
      "description": "serpentine: diameter of arcs",
      "value": 125
    },
    {
      "name": "labelsOnHover",
      "description": "milestone: show labels on hover only",
      "value": false
    },
    {
      "name": "sN",
      "description": "serpentine: number of arcs",
      "value": 2.2
    },
    {
      "name": "tC",
      "description": "ticks: number of axis ticks to display on the timeline",
      "value": 21
    },
    {
      "name": "tLO",
      "description": "ticks: the offset for the tick labels",
      "value": 8
    },
    {
      "name": "mO",
      "description": "milestone: the offset for the milestone markers",
      "value": 35
    },
    {
      "name": "sR0P",
      "description": "serpentine: percentage of width of canvas for the start of the timeline",
      "value": 0
    },
    {
      "name": "sLP",
      "description": "serpentine: percentage of total length of canvas for the end of the timeline",
      "value": 1
    },
    {
      "name": "annotationStart",
      "value": ""
    },
    {
      "name": "annotationEnd",
      "value": "Today"
    },
    {
      "name": "includeArrows",
      "value": true
    },
    {
      "name": "sT",
      "description": "serpentine: thicknes of the line",
      "value": 5
    },
    {
      "name": "domain",
      "init": "[year(now())-100, year(now())]",
      "description": "serpentine: manually set the domain extent for the timeline, otherwise set to null to have the domain calculated for you"
    },
    {
      "name": "sRange",
      "description": "serpentine: range for the serpentine scale",
      "update": "[sR0P*width,sL*sLP]"
    },
    {
      "name": "annotations",
      "description": "serpentine: annotations that appear at the start and end of the timeline",
      "update": "{start: (isValid(annotationStart) ? annotationStart : ''), end: (isValid(annotationEnd) ? annotationEnd : '')}"
    },
    {
      "name": "sD",
      "update": "[2, 2]",
      "description": "serpentine: dash array for the serpentine line"
    },
    {
      "name": "reverse",
      "description": "serpentine: boolean to indicate whether the scale for the timeline should be reversed",
      "value": false
    },
    {
      "name": "sPct",
      "description": "serpentine: percentage of width for the straight portions of the timeline",
      "value": 1,
      "update": "sPct < 0.25 ? 0 : sPct < 0.75 ? 0.5 : 1"
    },
    {
      "name": "sW",
      "description": "serpentine: horizontal length of straight segments",
      "update": "sPct*width"
    },
    {
      "name": "sL",
      "description": "serpentine: total length of line",
      "update": "(sN+1)*sW+(sN)*sH*PI/2"
    },
    {
      "name": "sA",
      "description": "serpentine: length of an arc segment",
      "update": "(sH*PI/2)"
    },
    {
      "name": "sWsA",
      "description": "serpentine: length of a line + arc segment",
      "update": "(sW + sH*PI/2)"
    },
    {
      "name": "sDomain",
      "description": "serpentine: domain for the serpentine scale",
      "init": "domain ? domain : [+extent(pluck(data('dataset'), 'domain'))[0], +extent(pluck(data('dataset'), 'domain'))[1]]"
    },
    {
      "name": "hoverFocus",
      "value": 0
    },
    {
      "name": "height",
      "description": "calculated height",
      "update": "extent(pluck(data('serpentine'), 'y'))[1]"
    }
  ],
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
      "interactive": false,
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

## Executive Summary

In 2024, we set our sights on transformative growth, focusing on innovation in product development and enhancing our customer engagement through digital platforms. Our roadmap is designed to align with our core mission while adapting to the dynamic market environment.

> 🌟 **NOTE**
> 
> This roadmap is a living document and may evolve based on market feedback and internal strategy shifts.

## Key Objectives

1. **Expand into New Markets**
   - Leverage market research to identify and enter two new international markets by Q3.
2. **Innovate Product Line**
   - Develop and launch three new products focusing on sustainability and technology integration.
3. **Enhance Customer Experience**
   - Implement a new customer service platform to improve response times and satisfaction rates.

## Milestones

### Q1

- **Market Research Completion**
  - Finalize research reports on potential new markets.
- **Product Development Kickoff**
  - Initiate development of our next-generation products.

> ⚠️ **WARNING**
> 
> Delays in market research may impact the timeline for new market entry.

### Q2

- **Customer Platform Beta Test**
  - Begin beta testing of our new customer service platform with select users.
- **Marketing Strategy Development**
  - Craft detailed go-to-market strategies for new products.

### Q3

- **New Market Entry**
  - Officially launch operations in one new international market.
- **Product Launch Prep**
  - Finalize and begin production for our first new product release.

### Q4

- **Second Product Launch**
  - Bring our second innovative product to the market.
- **Customer Experience Review**
  - Evaluate the impact of the new customer service platform and implement feedback.

## Strategic Initiatives

- **Sustainability Efforts**
  - Reduce carbon footprint by 25% through operational efficiencies and sustainable packaging.
- **Digital Transformation**
  - Accelerate digital transformation with a 50% increase in digital marketing investment.

> 💡 **TIP**
> 
> Engage with local communities in new markets to boost brand recognition and loyalty.

## Team and Roles

- **Project Managers** will oversee each major initiative, ensuring milestones are met on schedule.
- **The Marketing Team** will develop and execute our market entry and product launch strategies.
- **Customer Support** will manage the transition to the new platform, training staff, and ensuring a seamless customer experience.

## Conclusion

Our 2024 roadmap lays the foundation for a pivotal year in our company's history. By focusing on market expansion, product innovation, and enhancing the customer experience, we are poised for success. We look forward to the dedication and creativity of our team to bring these plans to fruition.

