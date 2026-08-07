---
title: "Blog 3"
date: 2026-08-04
weight: 3
chapter: false
pre: " <b> 3.3. </b> "
---

# USING AWS LOCATION SERVICE FOR MAPS AND LOCATION SEARCHES

Location is one of the most important factors influencing a tenant's decision when searching for a rental property. In addition to viewing property details, users also want to know where the property is located, what the surrounding area looks like, and whether it is convenient for daily travel. Therefore, the system needs to display property locations on an interactive map and allow property managers to select locations easily when creating or updating listings.

To meet these requirements, the project integrates **AWS Location Service** with **MapLibre GL JS** to provide map visualization and location search functionality. This approach takes advantage of AWS infrastructure, integrates seamlessly with other AWS services, and provides reliable geospatial capabilities for the application.

The location feature was implemented with the following approach:

- Use **AWS Location Service** to provide map rendering, place search, and geocoding services for converting addresses into geographic coordinates.
- Integrate **MapLibre GL JS** into the Next.js application to display interactive maps and property location markers.
- Allow property managers to search for an address and select a location directly on the map when creating or editing a property listing.
- Store geographic coordinates in PostgreSQL for future map visualization and location-based queries.
- Manage access to Amazon Location Service resources through **AWS IAM**, ensuring that only authorized services can access map resources.

During development, the team encountered several challenges, including configuring AWS IAM permissions, implementing **AWS Signature Version 4 (SigV4)** for authenticated requests, and reducing unnecessary geocoding requests while users typed addresses. These issues were resolved by applying the correct IAM configuration and using the **Debounce** technique in React, resulting in a responsive and reliable location search experience.

## Architecture Overview

![Overview](/images/3-BlogsPosted/Amazon_Location_Architecture.png)

## References

- https://docs.aws.amazon.com/location/
- https://maplibre.org/maplibre-gl-js/docs/
- https://docs.aws.amazon.com/location/latest/developerguide/integrating-with-amplify.html