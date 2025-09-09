# Nexus Real Estate Schema Log

Nexus is an open schema log structure for **property listings**.  
It provides a unified way to represent properties, agents, and listings across multiple real estate sites.

## Why Nexus?

Property data is siloed across agencies and portals. Nexus real estate schema logs provide:

- Unified **property schema**: define a listing once, syndicate everywhere.
- Cross-platform exposure: aggregate reviews (agents, properties, developments).
- SEO & AI benefits: enable rich results for real estate searches.
- Scalable: works for small brokers or enterprise-level agencies.

## Example Property Entry

```json
{
  "@context": "https://schema.org",
  "@type": "House",
  "identifier": "CNX-Real-001",
  "name": "3-Bedroom House in Chiang Mai",
  "description": "Modern 3-bedroom home with garden and pool.",
  "address": {
    "@type": "PostalAddress",
    "streetAddress": "123 Nimman Road",
    "addressLocality": "Chiang Mai",
    "addressRegion": "Chiang Mai Province",
    "addressCountry": "Thailand"
  },
  "offers": {
    "@type": "Offer",
    "priceCurrency": "THB",
    "price": "4500000",
    "url": "https://sawasdeerealty-example.com/listings/123"
  }
}
{
  "@context": "https://schema.org",
  "@type": "RealEstateAgency",
  "name": "Sawasdee Realty",
  "url": "https://sawasdeerealty-example.com",
  "telephone": "+66-88-888-8888"
}
```
## Usage

- Add logs for each property into /logs.
- Publish agent and agency profiles.
- Drop JSON-LD into listing pages for SEO.

## Roadmap

- Reference implementation: SawasdeeRealty
- Agent reviews + ratings
- Aggregated neighborhood data
- Thai-language mirror

## License

MIT License. Free to use, extend, or embed.
