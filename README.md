# Nexus — Real Estate Schema

Part of the [Schemalog](https://github.com/schemalog) framework.

## What This Is

A structured schema for Thai residential real estate that extends Schema.org Place and Offer with Thailand-specific attributes. Designed for agents, property portals, and AI-powered lead generation systems operating in the Bangkok market.

## Problem

Thai real estate listings lack standardized structured data, creating three major gaps:

1. **Location context is implicit:** Proximity to BTS/MRT stations, expressways, and flood-prone areas significantly affects property value but is rarely machine-readable. Agents calculate these manually for every listing.

2. **Property lifecycle attributes are missing:** NPA (bank repo) status, owner-direct vs agent-listed, FSBO classification — these signal-rich attributes exist in practice but not in standard schemas.

3. **Environmental risk is undisclosed or buried:** Seasonal flooding, industrial proximity, power line visibility — dealbreakers that kill transactions when discovered late. No structured way to surface them early.

Nexus provides a schema that makes Thai real estate data legible to both AI systems and automated workflows.

## Solution Architecture

```
Property Data     ──┐
Location Context  ──┼──> Nexus Schema ──> Structured Outputs
Market Signals    ──┤                      │
Environmental     ──┘                      ├──> Lead scoring systems
                                           ├──> Automated listings (TikTok, portals)
                                           ├──> Schema.org markup for SEO
                                           └──> Agent CRM integration
```

### Key Features

- **BTS/MRT proximity scoring** with walking distance and line information
- **Flood zone mapping** integrated with Bangkok district risk data
- **NPA property classification** with bank source tracking
- **FSBO detection** signals for co-listing opportunities
- **Environmental disclosure structure** for honest upfront risk communication
- **Price segment categorization** (luxury 9M+, mid-market 4-9M, affordable 2-3M)

## Implementation: Sawasdee Realty

**Business:** Independent real estate agency, Bangkok focus, 2-3M baht residential segment (sawasdeerealty.com)  
**Challenge:** Lead qualification, listing distribution, environmental disclosure consistency  

**Deployment Status:** In development.

**Planned Components:**

1. **Lead Pipeline Integration**
   - Scrapebox URL harvesting → Python extraction → Nexus schema → Claude scoring
   - Output: Ranked leads (FSBO, buyer-intent, NPA properties)
   - Target: 150-300 qualified leads per week

2. **Listing Schema for Multi-Channel Distribution**
   - Single Nexus input → formatted output for TikTok, DDProperty, Hipflat, Line OA
   - Environmental disclosure enforced at schema level
   - Reduces listing creation time from 30 min to 5 min per property

3. **Agent CRM Schema**
   - Track viewing history, buyer qualification status, deal lifecycle
   - Integrate with Claude for buyer-property matching
   - Flag repeat dealbreakers (e.g., buyer always walks on narrow stairs)

**Current Focus:** Lead pipeline first. Distribution templates second. CRM layer deferred.

## Schema Structure

Based on Schema.org Place and Offer with Thai market extensions:

```json
{
  "@context": "https://schema.org",
  "@type": "RealEstateListing",
  "name": "2-Story Townhouse, Don Mueang",
  "description": "3BR/2BA townhouse, 20 sq.wah, renovated...",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "Soi Example 12",
    "addressLocality": "Don Mueang",
    "addressRegion": "Bangkok",
    "postalCode": "10210",
    "addressCountry": "TH"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "13.9126",
    "longitude": "100.6069"
  },
  "offers": {
    "@type": "Offer",
    "price": "2200000",
    "priceCurrency": "THB",
    "availability": "https://schema.org/InStock",
    "seller": {
      "@type": "Person",
      "name": "Owner Name"
    }
  },
  "floorSize": {
    "@type": "QuantitativeValue",
    "value": "80",
    "unitCode": "MTK"
  },
  "numberOfRooms": 3,
  "numberOfBathroomsTotal": 2
}
```

### Thai-Specific Extensions

#### Transit Proximity

```json
"transitProximity": {
  "bts": {
    "station": "Don Mueang",
    "line": "Northern Extension",
    "walkingDistanceMeters": 800,
    "walkingMinutes": 10
  },
  "expressway": {
    "name": "Don Mueang Expressway",
    "distanceMeters": 1200,
    "driveMinutes": 5
  }
}
```

#### Environmental Disclosure

```json
"environmentalFactors": {
  "floodRisk": {
    "level": "low-seasonal",
    "season": "August-October",
    "affectedArea": "Adjacent soi, not property itself",
    "mitigationNotes": "Property elevated, no historical flooding on-site"
  },
  "industrialProximity": {
    "present": false
  },
  "powerLineVisibility": {
    "present": true,
    "location": "Side of property, visible from bedroom window",
    "distanceMeters": 30
  },
  "noiseFactors": [
    {
      "source": "Airport flight path",
      "severity": "moderate",
      "timeOfDay": "Morning and evening peaks"
    }
  ]
}
```

#### Market Classification

```json
"marketAttributes": {
  "propertyType": "townhouse",
  "priceSegment": "affordable",
  "listingType": "owner-direct",
  "npaStatus": {
    "isNPA": false,
    "bank": null
  },
  "fsboSignals": ["owner contact direct", "no agent commission mentioned"],
  "condition": "partially-renovated"
}
```

## Use Cases

### 1. Lead Scoring Pipeline

Automated system that evaluates scraped property listings:

```python
from nexus import LeadScorer

scorer = LeadScorer()

# Raw scraped data
raw_listing = {
    "url": "https://example.com/listing",
    "text": "ขายบ้าน 3 ห้องนอน ดอนเมือง 2.2 ล้าน เจ้าของขายเอง โทร 081-234-5678",
    "phone": "081-234-5678"
}

# Convert to Nexus schema and score
lead = scorer.process(raw_listing)

# Output includes:
# - owner_probability: 0.85 (high - "เจ้าของขายเอง" detected)
# - contact_confidence: 0.90 (phone number directly on page)
# - price_fit: 1.0 (within 2-3M target segment)
# - data_completeness: 0.60 (price/location/type present, size missing)
# - overall_score: 78/100
```

### 2. Multi-Channel Listing Distribution

Single schema input generates platform-specific formats:

```python
from nexus import ListingFormatter

formatter = ListingFormatter()

# Load property in Nexus schema
property_data = formatter.load("property_001.json")

# Generate outputs
tiktok_caption = formatter.to_tiktok(property_data)
ddproperty_listing = formatter.to_portal(property_data, platform="ddproperty")
line_message = formatter.to_line(property_data)

# All three include mandatory environmental disclosure if present
# All three formatted for platform constraints (character limits, etc.)
```

### 3. Environmental Risk Transparency

Enforced disclosure at schema level:

```python
from nexus import PropertySchema

# Schema validation fails if known risk is not disclosed
property = PropertySchema(
    price=2200000,
    location="Don Mueang",
    flood_risk_known=True,  # Detected from location data
    flood_disclosure=None   # Missing!
)

# Raises: EnvironmentalDisclosureError
# "Flood risk detected for location but not disclosed in schema"
```

## Technical Stack

- **Python** for data processing and validation
- **JSON Schema** for structure validation
- **Schema.org vocabulary** as base standard
- **Bangkok GIS data** for flood zone and transit mapping
- **Thai NLP** for FSBO and owner-direct signal detection

## Roadmap

- [ ] Complete core schema definition (in progress)
- [ ] Integrate Bangkok flood zone GIS data
- [ ] Build BTS/MRT proximity calculator
- [ ] Implement lead scoring logic
- [ ] Add listing formatter for major Thai portals
- [ ] Create agent CRM schema layer
- [ ] Add predictive pricing model based on location attributes

## Status

**Development:** Active. Core schema structure defined. Integration work in progress.

**Production Use:** Not yet deployed. Target: Q2 2025 for Sawasdee Realty pipeline.

**Note:** This schema is being built alongside an active real estate operation, so development priorities follow business needs rather than schema completeness.

## Why This Matters for AI

Structured real estate schemas enable:

1. **Lead scoring at scale** — automated evaluation of thousands of scraped listings
2. **Property recommendation engines** — matching buyers to properties based on structured preferences
3. **Price prediction models** — using location attributes and environmental factors
4. **Market trend analysis** — tracking NPA inventory, FSBO volume, price movements by district
5. **Honest disclosure systems** — forcing environmental risk transparency reduces transaction failures

Thai real estate data currently exists in unstructured text, images, and agent knowledge. Nexus makes it machine-actionable.

## Related Real Estate Projects

This schema supports several operational tools:

- **Scrapebox + Python Lead Pipeline** — harvests URLs, extracts contact data, scores via Nexus schema
- **Property Listing Copywriter Skill** — Claude skill that generates TikTok, portal, and Line OA listings from Nexus input
- **NPA Property Alert System** — monitors bank repo listings and sends alerts on new matches

These are separate tools that consume Nexus schema as input/output format.

## License

MIT

## Related Schemalog Projects

- [Schemalog INDEX](https://github.com/schemalog) — Framework overview
- [Codex (TarotSchema)](https://github.com/tarotschema) — Tarot reading schema
- [Omni] — Consumer product schema
- [Apex] — Tour and activity schema

## Contact

For questions about implementation or commercial use: [contact@schemalog.com]
